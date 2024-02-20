#include <iostream>
#include <fstream>
#include <opencv2/opencv.hpp>
#include <opencv2/face.hpp>
#include <filesystem>

using namespace cv::face;
using namespace std;
using namespace cv;

namespace fs = std::__fs::filesystem; // adding an alias for the namespace

bool isValidPath(const std::string& path);
bool performFaceRecognition(const vector<string> & photo_paths, Mat & live_video_frame);

int main(void){
    // checking for validity of the entered path
    std::string path;
    while (true) {
        cout << "Enter a valid path: ";
        cin >> path;
        cin.clear(); cin.ignore(); // clear input buffer

        if (isValidPath(path)) {
            cout << "The entered path is valid." << endl;
            break;
        } else {
            cout << "The entered path is invalid or does not exist. Please try again." << endl;
        }
    }
    // path to the folder containing photos you'd like to use for training
    string folder_path = "/path/to/photos/for/training";
    
    // collect all photo paths in the specified folder
    // only collecting '.jpg' or '.jpeg' or '.png'
    vector<string> photo_paths;
    for (const auto & entry : fs::directory_iterator(folder_path)) {
        if (entry.path().extension() == ".jpg" || entry.path().extension() == ".jpeg" || entry.path().extension() == ".png") {
            photo_paths.push_back(entry.path());
        }
    }

    // defining variables
    VideoCapture capture;
    Mat frame;
    CascadeClassifier cascade;
    double scale = 1;

    // loading the pre-trained classifier model
    cascade.load("haarcascade_frontalface_default.xml");
    // opening the web cam
    capture.open(0);
    
    if(capture.isOpened()){
        cout << "Face Detection Started...." << endl;
        bool fxn = true; 
        // while fxn is true perform face recognition
        while(fxn){    
            capture >> frame;           // continuiously capturing the frame 
            if(frame.empty()) break;    
            Mat frame1 = frame.clone(); // to avoid modifying original frame, we clone it

            // perform face recognition on the cloned frame and photo
            bool is_me = performFaceRecognition(photo_paths, frame1);
            if (is_me) {
                // Open the specified path if the face is recognized
                system(("open " + path).c_str());
                putText(frame1, "It's you!", Point(100, 100), FONT_HERSHEY_SIMPLEX, 1, Scalar(0, 255, 0), 2);
                fxn = false;    // the while loop shuts down after successful recognition
            
            // in case of failure the "Not you!" is printed on the frame   
            } else putText(frame1, "Not you!", Point(100, 100), FONT_HERSHEY_SIMPLEX, 1, Scalar(0, 0, 255), 2);
            
            imshow("Face Detection", frame1);   // display the frame 

            char c = (char)waitKey(10);        
            
            if( c == 27 || c == 'q' || c == 'Q' )   // if Q/q, exit
                break; 
        }
    } else {
        cout << "Could not Open Camera" << endl;
    }

    return 0;
}

bool isValidPath(const std::string& path){  // checking for path vaalidity
    std::ifstream file(path);
    return file.good();         // check if file opened successfully
}

bool performFaceRecognition(const vector<string> & photo_paths, Mat & live_video_frame){
    // creating a new LBPH face recognizer object that can be used for training
    // and recognizing faces in images
    Ptr<LBPHFaceRecognizer> recognizer = LBPHFaceRecognizer::create();

    // train the face recognizer with multiple photos
    vector<Mat> images;
    vector<int> labels;

    // loading and assigning labels to all photos
    int label = 0;
    for (const auto & photo_path : photo_paths) {
        // load the photo; loaded as gray-scale
        Mat photo_image = imread(photo_path, IMREAD_GRAYSCALE);
        if (photo_image.empty()) {
            cerr << "Error: Unable to load photo image: " << photo_path << endl;
            continue;
        }

        // Convert live video frame to grayscale
        Mat gray_frame;
        cvtColor(live_video_frame, gray_frame, COLOR_BGR2GRAY);

        // Add the photo and corresponding label to the training data
        images.push_back(photo_image);
        labels.push_back(label);
        
        label++;
    }

    // train the recognizer with the collected photos
    recognizer->train(images, labels);

    // convert live video frame to grayscale
    Mat gray_frame;
    cvtColor(live_video_frame, gray_frame, COLOR_BGR2GRAY);

    // perform face recognition on the live video frame
    int predicted_label = -1;
    double confidence = 0.0;
    recognizer->predict(gray_frame, predicted_label, confidence);

    if (predicted_label >= 0 && confidence < 50.0) return true; // face recognized as the person in the photo
    else return false; // Face not recognized    
}
