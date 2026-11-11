# Review of exercise 8 - Task 2

## 1. Mobile functionality

Checked:

* I opened my GitHub Pages application on my phone using:

  * Operating system: Android
  * Browser: Chrome
  * Application URL: https://johana-huzevkova.github.io/mlr-ex08/ and QR code



* Granted camera access and tested the application using an image of a real leaf from my gallery and a photo of a leaf displayed on another screen.



Findings and evidence:

* The application successfully accessed the phone's camera
* Successfully detected the leaf and displayed the predicted probabilities for all four classes: apple, cherry, chestnut and maple.
* No fixes were necessary



Screenshots:

* Photos from another display:

  * apple:
    ![Apple](images/Screenshot\_20261111\_095511.jpg)
  * cherry:
    ![Cherry](images/Screenshot\_20261111\_095410.jpg)
  * chestnut:
    ![Chestnut](images/Screenshot\_20261111\_095227.jpg)
  * maple:
    ![Maple](images/Screenshot\_20261111\_095316.jpg)
* Photo of real leaf from gallery:

![Cherry](images/IMG\_20261111\_080825.jpg)

![Cherry - probability](images/Screenshot\_20261111\_095645.jpg)





## 2. Prediction accuracy



Checked:

* I uploaded check\_image.jpg to my web application and compared the predicted probabilities with the reference values stored in check\_expected.json


Results:


| Class | notebook (expected) | Web application (actual) |
|-------|-----|-------|
| apple | 0.0000010138  | 0.0000010138  |
| cherry | 0.99998176098  | 0.99998182 |
| chestnut| 0.0000150619  | 0.000015062 |
| maple| 0.0000021077  | 0.0000021077 |



Findings and evidence:

* The results agree to three decimal places. The maximum absolute difference between the notebook and browser predictions is approximately 5.6 x 10e-8.
* The very small differences are caused by floating-point rounding during inference. The predicted class is the same in both cases: cherry



No fix was necessary. The predictions matched the notebook results to three decimal places and were also independently verified using ONNX Runtime CPU.



## 3. Preprocessing verification



Location:

* index.html: 125-140



Checked:

* I inspected the preprocessing implemantation in index.html and compared it with model\_info.json and the original notebook's image transformation:



Findings and evidence:

* central square crop: 125-126
* resize to 96x96: 130
* pixel scaling (0-1): 136-138
* normalization using mean and std: 136-138
* RGB channel order converted to CHW: 140
* input tensor shape (1x3x96x96): 140
* the preprocessing matches the original notebook



No fixes were necessary. All preprocessing operations matched the original notebook and model metadata



## 4. Privacy verification



Checked:

* I inspected the JavaScript code and checked the external URLs loaded by the application
* Reviewed how camera frames and uploaded images are processed



Findings and evidence:

* images and camera frames are processed locally in the browser. The camera stream is passed to video element and canvas. and model inference is performed using ONNX Runtiime WebAssembly.
* There is no form POST, image upload to the external service, analytics integration or transmission od user images in the application code
* The external URLs are used only to load the required JavaScript libraries and  WebAssembly runtime files.
* The QR code is also generated locally in the browser



No fixes were necessary. The application does not transmit photos or camera frames to an external server.





















