# DataBox Information Passing
## 1.0 Introduction
Message passing and data encryption have been used for quite some time now, however in our final project we developed a new form of message passing through encrypted pictures that we call data boxes. A databox is an N*N image where each cell in the image is part of an encrypted string. The encryption is done by converting each character in an input string into raw bits where each set of three bits corresponds to a particular color value. Then a cell in the databox, starting with the top left, is set to that color value such that each character in the input string becomes encrypted to a series of color values as adjacent cells inside the databox.

## 2.0 Goals for Project
There were three main goals for this project: create the data box, find the data box in a real world image, and translate the data box back to english. Using the OpenCV library as well as key concepts such as line detection, Hough transformations, and other forms of preprocessing, we were able to achieve all of these goals.

Creating the databox, as explained earlier, is essentially encrypting a string into an array of colored cells, surrounding the databox with an easily recognizable border, and setting the corner colors so that we can determine the orientation of the data box.

Finding the data box in a real world image proved to be exceptionally challenging. There were a lot of uncontrollable variables to consider, which includes: the background elements in each image, orientation of the data box, and the clarity/quality of the picture. Our initial solution to this problem was to create a nested black and white border surrounding the databox, with specific black to white ratios border lines that are unlikely to appear in nature (e.g. a ratio of 1:7:2 which would correspond to the first section of the border being 10 pixels thick, the second being 70 pixels thick, and the third border as 20 pixels thick). Conceptually speaking, this would allow us to iterate through the image and keep track of when a particular ratio exists. The idea with this approach is that no matter where the data box is in the image , iterating through it and comparing the ratio at every step will enable us to find it regardless of orientation and skew.

This approach alone however, was not good enough to identify the databox as it was apparent that the input images contain too many random elements, so using the ratios alone to find the databox would return inaccurate results. After many iterations of testing we found that using canny edge detection to detect all edges, walked from edge to edge, recorded the pixel ratio, and
 then compared the values to the true ratio to produce the best results. While this helped us find the edges, it did not help to find the corners of the databox, which are necessary for cropping and unskewing. To address this issue, we used the Hough line function on each individual edge point, took the average of those lines to find the best edge, and used the intersection of those lines to isolate the corners of the data box. Then we used the getPerspective and warpPerspective functions in OpenCV to unskew and crop the data box to prepare it for decryption.
 
One method that we attempted in the beginning to find the data box was to use sift however we quickly noticed that it was very slow and rather inefficient, so we decided to develop our ratio detection algorithm to improve the run speed.

Once the databox is found and cropped, it is sent to the readDatabox function to decrypt the color values. Reading the data box proved to be a difficult task that we had underestimated. When taking pictures with a camera phone, the color values often get washed out and tend not to be as sharp as the image originally was. As such, when we took a picture of the data box to send to our readDataBox function, the colors in each cell were inconsistent. This would mean that we can no longer just iterate through the data box cell by cell to check the average color value of each cell as sometimes the walls of each cell would be distorted as well. this resulted in inaccurate results for the colors of each cell. We noticed that the most accurate color value for all the pixels in each cell would realistically be held at the center of each cell. This meant that we would have to find the center coordinate of each cell and hold the color values in a matrix. We could then use the color values at each center coordinate to decrypt the message into bits, then characters, and then the original string. Using this method, we were able to accurately read and decrypt the data box.

## 3.0 Accomplishments
Although we were able to accomplish every goal that we had set out to do, some of our top accomplishments are our ratio identification system, data box object detection, and the evaluation of true color value despite noise and lack of image sharpness. The time complexity of our ratio identification system is O(n^2) as we are only parsing through the image in a top down and a left to right fashion. Our data box detection outside of our ratio identification system also has high accuracy and efficiency. We optimizing the Hough line function by averaging the ouput line to get an incredibly high accuracy for detecting the edges of our data box. Identifying the edges and corner points properly is an essential steps to finding a readable data box and it is because of the accuracy of our edge and corner detection that we are able to get an unskewed and cropped data box. By using the center of each cell to to pull a color patch and evaluate the true color value gave us accurate results for decrypting the data box. All of these accomplishments are unique aspects to our project that help us ultimately achieve all the goals that we had originaly set out to accomplish and more.

## 4.0 Results
As a team, we are extremely satisfied with the results of our project. Not only were we able to finish all of the goals we set for ourselves, but we developed a new method of detention that operated much faster than we expected. We were able to overcome many challenges and after many iterations of design and testing, we ended up with a final product that we can honestly say we are proud of. The product works incredibly well and under circumstances that we previously thought would be too hard to achieve, but we kept trying and have fully implemented the databox information passing design in a way that works consistently.

There are certainly downsides to our resulting implementation. For one, the detection of the data box walls is determined by the quality of the Canny edge detection. With further distances to the data box within an image, gaussian smoothing actually removes the smaller ratios and thus does not detect the edge. We could potentially make the distances further by making the ratio larger, but it already takes up a good amount of space. When we retrieve the unskewed databox from the image it is not perfect and subject to inconsistencies. These inconsistencies lead us to try and traverse the databox dynamically. Dynamically parsing the cells is why the checkered border was added. The checkered border allows the program to walk the edges to find the cell centers, then cross the row and column line to get the internal cell centers.

Below is a series of images describing the results of all of the steps associated with creating, finding, and reading a databox:
Step 1 & 2:

![Figure1](readme-images/Figure1.png)

Figures 1.1 and 1.2: These images represent the created databox and an image from a cell phone of the databox that is rotated and skewed. The second image will be used for the next few in the process.
  
 Step 3:
 
![Figure2](readme-images/Figure2.png)
 
 Figures 2.1, 2.2, 2.3, and 2.4: This collection of four images represents the detected edges of the databox from the photograph of the databox shown in figure 1. These edge points will be sent to the Hough space to get the best line associated with each respective edge.

Step 4:

![Figure3](readme-images/Figure3.png)

Figures 3.1 and 3.2: The databox after it has been extracted from the image in figure 1, it is rotated to the correct orientation represented in the right image.
  
 Step 5:
 
![Figure4](readme-images/Figure4.png)
 
 Figure 4: databox image after it has been transformed and cropped. Image is ready
to be read.

Step 6:

![Figure5](readme-images/Figure5.png)

Figure 5.1 and 5.2*: Find the center point of each cell (depicted visually by the red dots on each cell) and use the associated cell color to decrypt the message.
*Checkered border was added at a later date from the first five steps to improve consistency in reading the databox. All databoxes now include the checkered border.

## 5.0 Lessons Learned
There were so many hurdles necessary to overcome and finish this project, and as such our depth of knowledge with respect to computer vision, specifically the application of OpenCV methods in a c++ development environment, was incredibly broadened. However these lessons were not limited to coding operations and methods, we also learned a lot about image based data mining and the incredible number of struggles that come along with it. With what feels like the near infinite number of uncontrollable variables associated with image processing, a huge chunk of what we learned was related to accounting for and working around those inconsistencies.

Going into some specifics, when a picture of a databox is taken we - as the programmers - have little to no control over how that picture is taken or what the lighting conditions are when said photo is taken. As such, inorder to account for as many variations in images as possible, we implemented a plethora of checks and transformations to find and process the databox to a readable state.

Therefore, we can confidently say that the most important lesson we learned was that no matter how well you believe you have planned out your design and how successful your early stage testing goes, the final implementation will never work exactly as planned. There are too many aspects to account for and the best thing that we as developers can do is to recognize that as a truth and take that into consideration when planning. We were smart enough to start this project early and so even though we were met with a ton of issues, we gave ourselves enough time to address and test alternative implementations and designs as potential solutions to many of the struggles we faced. This was essential to getting to where we currently are and the product we have submitted.
## 6.0 Potential Next Steps and Additional Thoughts
If we were to continue with this project there are several aspects that we think could improve the overall design and usability of the databox message passing system. These changes include but are not limited to:

1. Implement live video message passing for strings that exceed the current max length of a databox - this would allow for a series of databoxes to be projected as a video and recorded on a second device to be interpreted back into the original message.
2. Implement live/active message passing - people could sit at two computers and communicate with one another via databoxes created from the messages typed by the users.
3. Apply a synchronization method based on camera proximity - this would account for distance issues and reduce the amount of data in a databox when distance makes the databox unreadable. This way, communicating devices would only send data in a readable size to prevent miscommunications.
These are just a few of the ideas for where this databox project can go, but we have many more including additional applications for out ratio detection methods. This project was a great introduction to the world of computer vision and its possibilities. There will always be room for improvement in the computer vision field, and as such we hope that our contributions will help push the boundaries of knowledge forward.

As a group, we all enjoyed this project. Having the opportunity to design an original program that was based on our ideas made this project so much more meaningful to us. This project has consumed a large portion of our time over the last few weeks, however we are thrilled with the results and are glad that we were able to get the program to the state it is in. Special thanks to Professor Clark Olson for teaching us about the ins and outs of computer vision, and to our friends and families who supported us throughout this process.

# Instructions for running program
### Program is meant to run on Visual Studio with openCV4.6
By Griffin Detracy, Camas Collins, and Rahul Pillalamarri

## Bat Requirements
Requirements for create DataBox bat
  format
    start "" [exe] [true] [cell width (# on 1 row)] [resolution width] [bits Per Cell]
  example
    start "" ../x64/Debug/Project3CV.exe 1 24 1024 1

Requirements for find DataBox bat (dynamically determines the bits percell when reading)
  format
    start "" [exe] [false] [cell width (# on 1 row)] [fileName]
  example
    start "" ../x64/Debug/Project3CV.exe 0 24 test0.jpg


## Existing Bat and Test Images
Each run_CreateDB_* bat creates a databox based on the data in InputStringDBCreation.txt
Each runtest*_FindReadDB bat attempts to solve a use case
  test 0 is the base use case of the entire input image being the databox
  test 1 at an odd angle by the corner
  test 2 at an odd angle over the image
  test 3 being far away (relatively)
  test 4 being rotated at an odd angle
  test 5 being only 1 bit per cell
  test 6 being only 2 bits per cell


## Extra Files
InputStringDBCreation.txt contains the data to be used when creating a databox
output.txt is generated after finding a databox, it contains the data found
createdDataBox.jpg is generated after the program creates a new databox

## General Instructions
Download all the files and then pull them from each folder so that the batch files can test the images correctly 
