# face_recognition
Access to file via facial recognition

In my testing I used 9 photos of myself to get satisfactory results. The photos were regular photos taken on my mac through photo booth. I displayed several different angles of myself.

Make sure you have "cmake" and "opencv" installed. I installed them through combination of pip and brew

I created 1 folder called opencv and inside that folder I have CMakeList.txt as well as my code. I also created a new folder called 'build' inside opencv folder. 'build' is my working directory.

Make sure you download "haarcascade_frontalface_default.xml". Place it in the 'build' folder. https://github.com/opencv/opencv/blob/4.x/data/haarcascades/haarcascade_frontalface_default.xml

Once you access the 'build' directory through terminal, type in and wait for execution of "cmake ..", "make" and then "./MyProject". 

Essentially you need to enter the path which you'd like to access on your computer. The path gets accessed if you face is recognized. The program then shuts down. In case of failure the program keeps running with the live video, which displays "Not You". To shut down the program press "Q" or "q". 

Note: You do need to provide the path to at least 9 photos of yourself for program to work. That'd be around line 32 or 33. 


