The function of our application is to prevent the misuse of AI by detecting and filtering deepfake images. It can be used in media to detect and filter AI-genereated content and mark it to differentiate it from non-AI content. It can help to protect the digital identity of users and prevent the spread of malicious visual misinformation.

Functional requirements:
3.1.1 Image Upload
The application shall allow users to upload an image for analysis.

3.1.2 Image Classification
The application shall analyze the uploaded image and classify it as either AI-generated or real.

3.1.3 Classification Result
The application shall display the classification result to the user after analyzing the image.

3.1.4 Confidence Score
The application shall display a confidence score associated with the classification result.

3.1.5 Classification Explanation
The application shall provide information about the visual characteristics or indicators that contributed to the classification result.

3.1.6 Image and Result Display
The application shall display the uploaded image alongside its classification result.

3.1.7 Upload Error Handling
The application shall notify the user when an uploaded image cannot be analyzed because of an unsupported file format, corrupted file, or another processing error.

3.1.8 Multiple Image Analysis
The application shall allow users to analyze another image without restarting the application.

3.1.9 AI Content Labeling
The application shall mark images identified as AI-generated with a visible AI-generated label.

3.1.10 Results Export
The application shall allow users to download or save the analysis result and its associated classification information.

Non-functional requirements:
3.2.1 Accuracy 
The application should accurately distinguish between real and AI generated images. It should report its accuracy, false positive and false negative rates because labeling a real image as AI generated or vice versa could have serious consequences.
3.2.2 Reliability
The application should provide similar results when the same image is analyzed under the same condition.
3.2.3 Security and Privacy
The application should protect user information and images. Uploaded images should not be shared with other users. Uploaded images should not be saved unless necessary.
3.2.4 Usability 
The application’s interface should be easy to use and navigate. A user with a non technical background should also be able to use it with ease.
3.2.5 Performance 
The application should analyze and return results within 3 minutes of upload under normal operating conditions 
3.2.6 Availability 
The application should be available 99% of the time under normal operating conditions 


