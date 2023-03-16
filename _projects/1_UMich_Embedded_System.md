---
title: "Embedded System"
excerpt: "Embedded System"
collection: projects
---


## Lab

### Basic for Bit manipulation

```c
// 1. set bits - use bitwise OR operator
number |= 1 << x; // 1 was shifted x positions
int num = 0;
num |= 1 << 3; // num = 8

// 2. clear bits - use vitwise AND operator
number &= ~(1 << x); // the x position was cleared
int num = 16;
num &= ~(1 << 4); // num = 0

// 3. toggle bits
number ^= 1 << x;
int num = 8;
num ^= 1 << 3; // num = 0

/* Macros*/
#define PORT_PCR_MUX_MASK  0x700u
#define PORT_PCR_MUX_SHIFT 8u
#define PORT_PCR_MUX_WIDTH 3u
#define PORT_PCR_MUX(x)    (((uint32_t)(((uint32_t)(x))<<PORT_PCR_MUX_SHIFT))&PORT_PCR_MUX_MASK)
```

### 1. DIP switches, LEDs

```c
void section1(){
    // set up pointers to ports and GPIO registers
    // base address for input and output port
    volatile uint32_t * const portD_PCR = (uint32_t *)(BASE_PORTD);
    volatile uint32_t * const portE_PCR = (uint32_t *)(BASE_PORTE);
    GPIO_mem * const gpioD = (GPIO_mem *)(BASE_GPIO + GPIOD_OFFSET);
    GPIO_mem * const gpioE = (GPIO_mem *)(BASE_GPIO + GPIOE_OFFSET);

    // Configure the LED pins
    for(index = 0; index < 5; index++){
        portD_PCR[index] = 0b001 << 8; // Configure pin mux to gpio
        gpioD->PDDR |= 0b1 << index; // Set the direction(PDDR) to output
    }
    // Configure the DIP pins
    for(index = 6; index < 14; index++){
        portE_PCR[index] = 0b001 << 8; // Configure pin mux to gpio
        gpioE->PDDR &= ~(1 << index); // Set the direction(PDDR) to input
    }

    while(1){
        value1 = 0;
        value2 = 0;
        sum = 0;

        // 5-8 of the DIP switches - first 4 of the switches
        value1 = (gpioE->PDIR >> 10) & 0b1111;
        // 1-4 of the DIP switches - second 4 of the switches
        value2 = (gpioE->PDIR >> 6) & 0b00001111;

        sum = value1+ value2;
        // change the LED lights according to the sum
        gpioD->PDOR = sum;
    }
}
```

```c
void section2(){
  /* code for section 2 */
  uint16_t sum = 0, value1 = 0, value2 = 0;
  uint16_t temp;
  int index;

  // call initialization functions with port(A-D) and pin(0-15)
  for(index = 0; index < 16; index++){
    initGPDI(DIP_BASE[index], DIP[index]);
    initGPDO(LED_BASE[index], LED[index]);
  }

  while(1){
    // read the DIP switches and write to LEDs
    value1 = 0;
    value2 = 0;
    // read E6-E9
    for (index = 9; index >= 6; index--) {
      value1 += (readGPIO(4, index) << (index - 6));
    }
    // read E10-E13
    for (index = 13; index >= 10; index--) {
      value2 += (readGPIO(4, index) << (index - 10));
    }
    sum = value1 + value2;

    // write the pins
    temp = 0;
    for(index = 4; index >= 0; index--){
      temp = (sum >> index) & 0b1;
      writeGPIO(3, index, temp);
    }
  }
}
```

### 2. Quadrature Decoding with the FlexTimer

FTM have many registers, and each are 32 bits in width.  
Every bitfield have its own function, whether its enable bits or setting values.

| QDCTRL Register          |  FTM block diagram
:-------------------------:|:-------------------------:
![Quadrature Decoder Control And Status (QDCTRL) Register](/images/projects/UMich/Embedded_System/QDCTRL_register.png)     |  ![FTM block diagram](/images/projects/UMich/Embedded_System/FTM_block_diagram.png)

```c
/* qd.c */
/******************************************************************************
* Function: init_QD
* Description: Initializes FlexTimer for Quadrature Decoding
******************************************************************************/
void initQD()
{
    /* Initialize Phase A and B input PCR */
    QD_PHA_PORT->PCR[QD_PHA_PIN] |= PORT_PCR_MUX(0b110); // Phase A
    QD_PHB_PORT->PCR[QD_PHB_PIN] |= PORT_PCR_MUX(0b110); // Phase B

    /* Set up FTM2 for Quadrature Decode */
    // Find the correct FTM -> find the register -> change to desired value
    FTM2->SC &= FTM_SC_FLTPS_MASK; // initialize scalar to 1
    FTM2->MODE |= FTM_MODE_WPDIS_MASK; /* Disable write protection (should already be disabled) */
    FTM2->MOD = 0xFFFF;
    FTM2->CNTIN = 0x0000;
    FTM2->QDCTRL = 0x1;   /* Enable QD mode */
    FTM2->CONF |= FTM_CONF_BDMMODE(0b11); /* Optional: enable in debug mode */
    return;
}

/******************************************************************************
 * Function:    updateCounter
 * Description: Returns an updated counter value that keeps track of absolute
 *              wheel position
 ******************************************************************************/
int32_t updateCounter()
{ /* setup */
    PREV_QDPC = CURR_QDPC;
    CURR_QDPC = FTM2->CNT & FTM_CNT_COUNT_MASK;
    TOTAL = TOTAL + (int16_t)(CURR_QDPC - PREV_QDPC); // correct
//    TOTAL = TOTAL + (CURR_QDPC - PREV_QDPC);  // faulty casting
    return (TOTAL);
} /* update wheel position */

/******************************************************************************
 * Function:    updateAngle
 * Description: Returns the angle of the wheel
 ******************************************************************************/
float updateAngle(){
    TOTAL = updateCounter();
    return TOTAL * 0.36 /4; // 4 is 
};   /* convert counts to angle */

```

### 3. Analog-to-Digital Conversion (ADC)

| SC3 Register          |  ADC block diagram
:-------------------------:|:-------------------------:
![Status and Control Register 3 (SC3) Register](/images/projects/UMich/Embedded_System/SC3_register.png)     |  ![ADC block diagram](/images/projects/UMich/Embedded_System/ADC_block_diagram.png)

```c
void init_ADC0_single(void)  {
    /* Table 27-9 Peripheral module clocking */
    /* 29.6.19 PCC ADC0 Register (PCC_ADC0) */
    PCC->PCCn[PCC_ADC0_INDEX] |= PCC_PCCn_CGC(0b0);    /* Disable clock to change PCS */
    PCC->PCCn[PCC_ADC0_INDEX] |= PCC_PCCn_PCS(0b001);  /* Select clock option 1 */
    PCC->PCCn[PCC_ADC0_INDEX] |= PCC_PCCn_CGC(0b1);    /* Enable clock */

    /*42.4.2 - ADC Status and Control Register 1 (SC1AA - SC1Z)*/
    ADC0->SC1[0] |= ADC_SC1_ADCH(0b11111);  /* Disable Module */
    ADC0->SC1[0] |= ADC_SC1_AIEN(0b0);      /* Disable interrupts */
    
    /*42.4.3 - ADC Configuration Register 1: CFG1 */
    ADC0->CFG1 |= ADC_CFG1_ADICLK(0b00);    /* Alternate clock 1 */
    ADC0->CFG1 |= ADC_CFG1_MODE(0b01);      /* 12-bit conversion (there are 8-bit, 10-bit) */
    ADC0->CFG1 |= ADC_CFG1_ADIV(0b00);      /* Prescaler=1 */
    
    /*42.4.4 - ADC Configuration Register 2: CFG2 */
    ADC0->CFG2 |= ADC_CFG2_SMPLTS(0b1100);  /* set sample time to 13 ADC clks */
    
    /*42.4.7 - Status and Control Register 2: SC2 */
    ADC0->SC2 |= ADC_SC2_ADTRG(0b0);        /* Software trigger, a conversion is initiated following a write to SC1A */
    ADC0->SC2 |= ADC_SC2_REFSEL(0b00);      /* use voltage reference pins VREFH & VREEFL */
                                    
    /*42.4.8 - Status and Control Register 3: SC3 */
    ADC0->SC3 |= ADC_SC3_CAL(0b0);         /* Do not start calibration sequence */
    ADC0->SC3 |= ADC_SC3_ADCO(0b0);        /* One conversion performed (single mode) */
    ADC0->SC3 |= ADC_SC3_AVGE(0b0);        /* HW average function disabled */
}


uint8_t ADC0_complete(void)  {
    /* 42.4.2.4 - COCO flag */
    return (((ADC0->SC1[0]) & ADC_SC1_COCO_MASK) >> ADC_SC1_COCO_SHIFT);  /*return COCO flag*/
}


uint32_t read_ADC0_single(uint16_t inputChannel)  {
    uint16_t adc_result=0;
    
    /* inform students of the mask and functions in header for ADCH */
    ADC0->SC1[0] &= ~(ADC_SC1_ADCH_MASK);          /* Clear prior ADCH bits */
    ADC0->SC1[0] |= ADC_SC1_ADCH(inputChannel);    /* Initiate Conversion */

    while(ADC0_complete() == 0); // wait for completion
    
    /* 42.4.31.2 - Data Result Register*/
    adc_result = ADC0->R[0];      /* For Software trigger mode, R[0] is used */
    
    /* Convert result to mv for 0-5V range */
    /* Hint: can this be done without floating point math for speed? */
    return  (uint32_t)(adc_result * 5000 / 4095);
}
```

### 4. Pulse Width Modulation (PWM)

### 5. Interrupts and Frequency Analysis

### 6. Virtual Worlds with Dynamics

### 7. Control Area Network (CAN)

### 8. Autocode Genegration

## Final Project
