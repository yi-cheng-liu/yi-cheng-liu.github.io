---
title: "Programming for Scientists and Engineer"
excerpt: "c++ programming for solving issues
<br/>
<img src='/images/projects/UMich/Programming_for_scientist_engineer/project3_result.jpg'>"
collection: projects
---

:computer: [Github repo](https://github.com/yi-cheng-liu/Programming-for-Science-and-Engineering)

## Project 1 - Accure Interest calculator

This project involves implementing a program to calculate interest for an investment account with monthly accruals. The balance of the account cannot be changed through deposits or withdrawals. Three different interest rates are applied depending on the account balance: a minimum rate, a standard rate, and a maximum rate.

The key points of the project are:

* Interest is accrued monthly, and the length of the month does not affect the interest amount.
* Only one interest rate is used during a month, based on the balance at the start of the month.
* The interest rates are determined by the account balance, with higher balances receiving a higher rate. The minimum rate is applied for balances under \$1000, the standard rate for balances between \$1000 and \$15000, and the maximum rate for balances over \$15000.

## Project 2 - Pixel and image representation

This project involves implementing three C++ classes to represent colors, images, and locations within an image. Colors are described using RGB values, and images are represented as two-dimensional arrays of pixel colors. Locations within an image are identified by row and column indices.

The key points of the project are:

* Colors are represented using RGB values, with a maximum allowed value of 1000 and a minimum allowed value of 0.
* Images are represented as two-dimensional arrays of pixel colors, with rows and columns as the two dimensions.
* Locations within an image are identified by their row and column indices.

## project 3 - Image modification (bounding boxes, pattern, and images)

In this project, you will be working with a simple image format called PPM (Portable Pixel Map). PPM files are text files that contain information about the color values of each pixel in an image. The first line of the file contains the file type (P3 for PPM), followed by the image width and height, and the maximum color value (255). The rest of the file contains a list of the red, green, and blue color values for each pixel, one pixel per line.

Your task is to write a program that can read in a PPM file, modify the image in a few specific ways, and then write the modified image back out to a new PPM file. Specifically, your program should be able to:

* Read in a PPM file and create a Color Image object to represent the image.
* Modify the image by either:
  * Drawing a rectangle with a specified color at a specified location and size
  * Replacing all pixels of a specified color in the image with a specified pattern
* Write the modified image out to a new PPM file.

Additional classes to represent the rectangle and the pattern was created. I also use dynamic allocation of arrays to store the pixel values of the image and the pattern. Additionally, any potential errors that may occur during file input/output have to be handled.

### :star:Error Handling

1. Unable to read ppm file
    * ppm is not P3
    * Height and width doesn't match the pixels
    * color range is over 255

2. Input of pixel location wrong
    <details>
    <summary>The input pixel is under 0 or over the image boundary</summary>
    In this case, (449, 599) is valid and (450, 600) is invalid
    </details>
    <details>
    <summary>The lower right is smaller than the left upper</summary>
    upper left(10, 10) and lower right(8, 8) -> cause ERROR!
    </details>

### :star:test.sh

This is the test file to check all invalid input and the boundaries for the image.

#### test 1 - check the three methods of rectangle

```bash
# test out the rectangle
rm -f 1_rectangle_test_1.ppm 1_rectangle_test_2.ppm 1_rectangle_test_3.ppm
cat 1_rectangle_1_test.txt | ./project3.exe           # should have 6 ERROR!
cat 1_rectangle_2_test.txt | ./project3.exe           # should have 6 ERROR!
cat 1_rectangle_3_test.txt | ./project3.exe           # should have 4 ERROR!
```

#### test 2 - pattern check(ohdeerPattern_rectangle)

```bash
# test out the pattern image
rm -f 2_pattern_test.ppm
cat 2_pattern_test.txt | ./project3.exe               # should have 6 ERROR!
```

#### test 3 - image check(topHat_rectangle)

```bash
# test out the image
rm -f 3_image_test.ppm
cat 3_image_test.txt | ./project3.exe                 # should have 6 ERROR!
```

#### test 4 - print out the given test

```bash
# formal test
rm -f new1.ppm new2.ppm new3.ppm new4.ppm new5.ppm
cat 4_formal_test.txt | ./project3.exe
```

### Result

![deer reuslt](/images/projects/UMich/Programming_for_scientist_engineer/project3_result.jpg)

## project 4 - Linked list (Sorted linked list, implement stack and queue with linked list)

This project involves implementing a doubly-linked list data structure that is always maintained in sorted order, along with a node class that can be used in other data structures. The node class should not be aware of the list and can be used independently.

## project 5 - Event driven simulation

The goal of this project is to implement an event-driven simulation of traffic flow through a 4-way intersection that is managed by a traffic light. The simulation should include cars arriving at the intersection traveling in all four directions, as well as the light changing state throughout the simulation. The project will be split into two phases. In the first phase, the SortedListClass and the FIFOQueueClass developed in project 4 will be updated to be templated classes. In the second phase, an event-driven simulation of cars traveling through an intersection will be developed using the templated data structures. The simulation will read in 15 control parameters from a text file and output some basic statistics about the traffic flow.
