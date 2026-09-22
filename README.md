The function of our application is to prevent the misuse of AI by detecting and filtering deepfake images. It can be used in media to detect and filter AI-genereated content and mark it to differentiate it from non-AI content. It can help to protect the digital identity of users and prevent the spread of malicious visual misinformation.

User categories:
The application serves four categories of users. Each category is distinguished by the goal that brings the user to the application, the technical background they bring, the volume of images they analyze, the urgency of the decision they are making, and the consequences that follow from a misclassification. Each category is also tied to the requirements in Section 3 that exist primarily to serve it.

2.1 General Consumers of Online Media
A member of the general public who encounters an image on a social platform, in a messaging thread, or in an online article and wants to know whether it is authentic before believing it or resharing it. This is the user whose digital identity and information environment the application is ultimately protecting.
The typical user of this category is motivated by curiosity and self-protection. They want to avoid being personally deceived and to avoid passing misinformation to others. Their technical background is low to none. They have no training in digital forensics or machine learning and cannot interpret raw technical output. They analyze images at a low and sporadic rate, typically one image at a time and only a handful of times a month, usually prompted by something that struck them as suspicious. They work predominantly on mobile devices, often on a cellular connection, and typically from a screenshot or re-shared copy rather than an original file. Their tolerance for complexity is very low. If the result is not understandable within a few seconds, this user abandons the tool. The consequence of an error for them is moderate and personal. A wrong answer may lead them to believe or spread a false image, but the harm is generally contained to their own social circle.
This user is the reason the result must be presented in plain terms (3.1.3) with a confidence score they can weigh (3.1.4) and an explanation of the visual indicators behind it (3.1.5), and the reason the interface must be usable by someone with a non-technical background (3.2.4). Because they frequently upload screenshots and re-compressed files, they are also the category most likely to trigger the error handling required by 3.1.7.

2.2 Journalists and Fact-Checkers
A reporter, editor, or staff member of a fact-checking organization who must verify a photograph before publishing or citing it. Verification is an explicit, accountable step in their professional workflow.
The typical user of this category is motivated by professional accountability. Publishing an AI-generated image as genuine is a credibility failure for the user and their organization and may require a public correction. Their technical background is moderate. They are not machine-learning practitioners, but they are experienced at evaluating evidence and accustomed to reasoning about uncertainty, and they treat the application's output as one input to a judgment rather than a final answer. Their analysis volume is moderate to high and bursty, spiking around breaking news when many images must be checked at once under deadline pressure. Their time sensitivity is high, because a verdict that arrives after the publication deadline has no value. They must be able to justify the decision to an editor, a legal team, or the public, which means the analysis has to survive as a record rather than a transient screen result. The material they handle is highly sensitive, since images frequently come from confidential sources and must not be retained or exposed by the tool. The consequence of an error is severe and public. A false negative allows a fabricated image into the public record, while a false positive may suppress a genuine and newsworthy photograph.
Their need for a durable, defensible record is the reason for results export (3.1.10), and their need to weigh the tool against other evidence is why the confidence score (3.1.4) and explanation (3.1.5) matter as much as the verdict itself. Because both kinds of error carry real cost for them, they are the reason the application must report false positive and false negative rates rather than a single accuracy figure (3.2.1). Their deadline pressure motivates the response-time limit (3.2.5), and their confidential sourcing motivates the privacy constraints in 3.2.3, particularly the rule that uploaded images are not shared and not retained unless necessary.

2.3 Content Moderators and Platform Trust and Safety Staff
A moderator employed by a social media platform, online marketplace, or hosting provider who reviews images at scale and applies platform policy. This is the user who performs the filtering and marking described in Section 1 as an operational job rather than an occasional check.
The typical user of this category is motivated by policy enforcement at scale under a quota, and their performance is measured in decisions per hour as well as in decision quality. Their technical background is moderate and tool-centric. They are trained on internal tooling and policy rather than on detection science, but they use such tools continuously and become fluent at reading their output. Their analysis volume is by far the highest of any category, since they process images continuously throughout a shift. The application's output is one signal feeding a larger enforcement decision, so consistency matters as much as correctness, because inconsistent outcomes across similar cases expose the platform to accusations of arbitrary enforcement. Their interface priority is speed and throughput over explanatory depth, since they need to reach a decision without context-switching. Their dependence on availability is high, because moderation queues do not pause and downtime creates a backlog rather than merely an inconvenience. The consequence of an error is severe and systematic, because this user applies the tool thousands of times and a small bias in the classifier is amplified into a large number of wrongful takedowns or missed violations.
Their throughput is the reason the application must support analyzing another image without restarting (3.1.8) and must display the image alongside its result so a decision can be made in one view (3.1.6). Their enforcement role is the reason AI-generated images must carry a clear visible label (3.1.9). Consistency across repeated similar cases is the operational meaning of the reliability requirement (3.2.2), and their uninterrupted queues are the basis for the availability requirement (3.2.6).

2.4 Educators and Students
An instructor teaching media literacy, digital forensics, or AI ethics, and the students in such a course. They use the application as a teaching instrument rather than as a verification service.
The typical user of this category is motivated by learning and demonstration. The goal is to understand how AI-generated images can be recognized, and the verdict on any single image is secondary to the reasoning behind it. Their technical background is highly variable, ranging from students with none at all to instructors with formal computer science training, and no other category spans so wide a range within a single session. Their usage pattern is deliberately adversarial and exploratory. They intentionally submit edge cases such as heavily compressed images, digital artwork, hand-drawn illustrations, and photographs with unusual lighting, specifically to find the limits of the classifier. They are also the only category that routinely knows the correct answer in advance and is checking the tool rather than the image. Their analysis volume is concentrated in bursts, since an entire class may use the application simultaneously during one lab session, producing a short and sharp load spike. Unlike other categories, this group treats a low confidence score as an interesting result worth discussing rather than a failure of the tool. The consequence of an error is low in immediate terms, since no publication or enforcement decision depends on the outcome, but a persistently wrong tool teaches an incorrect mental model of how AI-generated content is detected.
The explanation of visual indicators (3.1.5) is the feature this category values above all others, since for them the reasoning is the lesson and a bare label teaches nothing. Their habit of submitting edge cases makes clear error messaging (3.1.7) especially important, and their rapid comparison of many images in one session depends on analyzing images consecutively without restarting (3.1.8).

2.5 Summary of Distinctions
General consumers seek to avoid being deceived, bring a low technical background, analyze images at a low and sporadic rate under little time pressure, value a plain verdict (3.1.3) above all, and bear a personal cost when the result is wrong.
Journalists seek to verify images before publishing, bring a moderate technical background, analyze images at a moderate and bursty rate under very high time pressure, value an exportable record (3.1.10) above all, and bear a public and reputational cost when the result is wrong.
Content moderators seek to enforce policy at scale, bring a moderate and tool-centric background, analyze images at a very high and continuous rate under high time pressure, value a fast and consistent verdict (3.2.2) above all, and bear a systematic and amplified cost when the result is wrong.
Educators and students seek to teach and learn how detection works, bring a highly variable technical background, analyze images in class-sized bursts under little time pressure, value the explanation (3.1.5) above all, and bear a pedagogical cost when the result is wrong.

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


