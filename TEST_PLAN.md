Detection and Filtering of AI-Generated Images
Test Plan

Team
Team Veritas

Team Members
Edwin Huallpa
Yslam Ismailov
Aakash Nagarahalli
Numa Fatima

Course
CS 487 - Software Engineering I

Date
October 4, 2026


1. Summary of the Proposed Testing Strategy

This test plan covers the AI-generated image detection application described in the
requirements report. Testing is organized around the four user categories identified in
that report - general consumers, journalists and fact-checkers, content moderators, and
educators and students - because each category exercises the application differently and
bears a different cost when a result is wrong. The plan is written for a working
prototype that will be demonstrated in class, so every case below is one the team can
execute by hand with the equipment available to it.

1.1 Scope

The strategy covers three layers of the application. The classification engine is tested
for correctness, for measured accuracy against a labeled corpus, and for consistency
across repeated runs. The user interface is tested for the presentation of results,
labeling, explanation, and export. The application as a whole is tested for its handling
of invalid input and for its behavior under the degraded conditions a real user is likely
to encounter.

1.2 Normal and Exceptional Cases

Normal cases establish that the application behaves correctly on the input it was
designed for: a clear, reasonably sized image that is either plainly synthetic or plainly
authentic. These cases verify classification, confidence reporting, explanation, labeling,
side-by-side display, and export.

Exceptional cases establish that the application fails safely when it cannot do its job.
These include unsupported file formats, corrupted and truncated files, zero-byte files,
files that exceed the upload size limit, and images whose quality is too low to support a
confident determination. The governing principle for every exceptional case is that the
application must prefer an honest refusal to a confident guess, because a wrong
high-confidence answer is more damaging to every user category than an admission of
uncertainty.

1.3 Boundary and Edge Cases

Boundary cases probe the conditions where correct behavior is most likely to break down:

- Image quality boundary. Heavily compressed and downscaled images, including screenshots
  of screenshots, where the forensic signal the classifier depends on has been partially
  destroyed by re-compression. This is the ordinary condition for general consumers, who
  usually arrive with a reshared copy rather than an original file.
- Confidence boundary. Images that produce a classification near the decision threshold,
  verifying that the reported confidence is proportional to the evidence and that a
  borderline result is not presented with unwarranted certainty.
- Input size boundary. Files immediately below and immediately above the accepted upload
  size limit, and images at the extremes of supported resolution.
- Performance boundary. Timing of the largest supported input against the three-minute
  limit stated in requirement 3.2.5.
- Ambiguity boundary. Human-made digital artwork, heavily filtered photographs, and
  photographs of screens - inputs where a reasonable person could disagree about the
  correct label, and where the application should express uncertainty rather than resolve
  the ambiguity arbitrarily.

1.4 Test Data

Testing uses a purpose-built corpus of 60 images specified in `testdata/README.md` and
enumerated in `testdata/manifest.csv`. The corpus is deliberately modest in size, since it
must be assembled and labeled by hand by a four-person team, and it is designed so that
its size does not undermine the measurements taken from it.

The corpus contains 54 classifiable images, balanced at 27 AI-generated and 27 authentic,
plus 6 deliberately invalid files used by the error-handling cases. The balance matters:
because neither class is favored, a classifier that simply guessed one label every time
would score 50% on this corpus rather than an inflated figure, which is what makes the
accuracy measurement in case 2.8 meaningful.

Ground truth is established by provenance rather than by inspection. An image counts as
AI-generated only when a team member generated it, and as authentic only when a team
member photographed it. No image is labeled by judging what it looks like, because
labeling by appearance would make any accuracy measurement circular - the team would be
testing the classifier against its own guesses. Degraded variants are produced by
re-compressing known images, so a degraded image inherits the ground truth of the original
it was derived from.

The image files themselves are not committed to the repository. They include personal
photographs belonging to team members, and redistributing images drawn from public
datasets may conflict with the terms those datasets are released under. The specification
and manifest are committed instead, which is sufficient for any team member to reconstruct
the corpus.

1.5 Test Environment

The prototype has not yet been implemented, so the environment below describes the
configuration the team intends to test on and will confirm once the prototype exists.

- Desktop: current versions of Chrome and Firefox on macOS and Windows, on a standard
  broadband connection. This is the primary environment for cases involving sustained use,
  export, and repeated analysis.
- Mobile: current mobile Safari and Chrome on a phone over a cellular connection. This
  environment matters because the general consumer category works almost entirely from
  mobile devices and from reshared screenshots rather than original files.
- Network conditions: normal broadband, throttled mobile, and interrupted connection, the
  last used to verify that a failure during upload or analysis is reported rather than
  silently dropped.

1.6 Acknowledged Limitations

Three requirements cannot be fully verified by a four-person team working on a course
prototype, and the plan states this rather than implying coverage it does not have.

- Availability (3.2.6). A 99% availability figure is a claim about long-run behavior and
  cannot be established within a semester. Case 2.9 substitutes a bounded observation
  window, which can detect gross unavailability but cannot confirm the annual figure.
- Accuracy (3.2.1). The accuracy, false positive, and false negative rates measured in
  case 2.8 are estimates drawn from 54 images. They are sufficient to detect a badly
  performing classifier and to satisfy the requirement that the application report these
  rates, but the confidence interval around them is wide, and they should be reported as
  measurements on a named corpus rather than as general performance claims.
- Security and privacy (3.2.3). Case 2.7 verifies the privacy behavior that is observable
  from outside the application. A full verification would require inspecting server-side
  storage, which is not available to the team at the time of writing.


2. Detailed Test Cases

2.1 Standard AI Image Classification and Labeling

This case evaluates the system's ability to classify a high-quality AI-generated image
and present the result understandably. This core workflow matters to general consumers,
who need a plain-language answer, and to content moderators, who rely on clear visual
labeling to enforce policy.

Requirement: 3.1.1, 3.1.2, 3.1.3, 3.1.4, 3.1.5, 3.1.6, 3.1.9, 3.2.4
Test data: any image from subset A
Initial state: The application is loaded on the main interface under normal server load,
with no image previously uploaded in the session.
Execution steps:
  1) Upload a high-resolution AI-generated image in JPEG format.
  2) Observe the processing indicator while analysis runs.
  3) Review the results screen.
Expected final state: The application displays the uploaded image alongside a result
stating that the image is AI-generated. A visible AI-generated label is applied, a
confidence score is shown, and a plain-language explanation identifies the visual
indicators behind the determination. No technical jargon is required to understand the
verdict.

2.2 Standard Authentic Image Classification and Results Export

This case ensures that journalists and fact-checkers can verify an authentic image under
time pressure and leave with a defensible record of the analysis.

Requirement: 3.1.1, 3.1.2, 3.1.3, 3.1.10, 3.2.5
Test data: any image from subset B
Initial state: The user is on the main interface with a standard broadband connection.
Execution steps:
  1) Upload an authentic, unedited photograph.
  2) Start a timer at upload and stop it when the result appears.
  3) Use the export control to save the analysis.
Expected final state: The application returns an authentic classification within the
three-minute threshold. The exported record contains the image, the classification, the
confidence score, and the explanation, and it can be opened independently of the
application.

2.3 Error Handling for Unsupported and Corrupted Files

This case verifies that invalid input is rejected cleanly. It matters most for general
consumers, who work from mobile devices and may select the wrong file.

Requirement: 3.1.7, 3.2.4
Test data: F-01 (PDF renamed to .jpg), F-02 (truncated JPEG), F-06 (corrupted header)
Initial state: The user is on the main upload screen.
Execution steps:
  1) Attempt to upload F-01 and observe the response.
  2) Repeat with F-02.
  3) Repeat with F-06.
Expected final state: Each upload is rejected with a clear, non-technical message naming
the problem as an unsupported or unreadable file and inviting the user to try another
image. The application remains responsive after each rejection, and no partial or
placeholder result is displayed.

2.4 Input Size Boundary

This case probes the upload size limit from both sides, confirming that the boundary is
enforced and reported rather than silently truncating or hanging.

Requirement: 3.1.1, 3.1.7, 3.2.5
Test data: F-03 (zero-byte file), F-04 (oversized image), plus the largest image in
subset A that is within the limit
Initial state: The user is on the main upload screen with the documented size limit known.
Execution steps:
  1) Upload the zero-byte file F-03.
  2) Upload the oversized file F-04.
  3) Upload the largest within-limit image and time the analysis.
Expected final state: The zero-byte and oversized files are both rejected with specific
messages distinguishing an empty file from one that is too large. The within-limit image
is accepted and returns a result within the three-minute threshold, establishing that the
limit is enforced at the boundary rather than approximately.

2.5 Multiple Consecutive Image Analysis

This case validates the high-throughput workflow required by content moderators, who
process images continuously throughout a shift.

Requirement: 3.1.8, 3.2.5
Initial state: The user has completed one analysis and the result is on screen.
Execution steps:
  1) Use the control to analyze another image without refreshing or restarting.
  2) Upload a second image from a different subset than the first.
  3) Repeat for a third and fourth image without restarting at any point.
Expected final state: Each analysis returns correctly and within the performance
threshold, the interface returns to a ready state between images, and no result from a
previous image persists into the next. Performance does not degrade measurably across the
sequence.

2.6 Reliability and Consistency Across Sessions

This case confirms that the classifier produces stable outcomes for identical input.
Inconsistency would expose moderators to accusations of arbitrary enforcement and would
undermine a journalist's ability to rely on a prior result.

Requirement: 3.2.2
Test data: an image from subset E, chosen because borderline inputs are where instability
would appear first
Initial state: The user is on the main interface.
Execution steps:
  1) Upload the image and record the classification, confidence score, and explanation
     verbatim.
  2) Close the session entirely and reopen the application.
  3) Upload the identical file under the same network conditions.
  4) Repeat once more on a different supported browser.
Expected final state: All three runs return the same classification. The confidence score
is identical or differs by a margin small enough that it would not change the verdict, and
the explanation cites the same visual indicators.

2.7 Privacy of Uploaded Images

This case verifies the privacy behavior observable from outside the application, which
matters to journalists working from confidential source material. It is scoped to what the
team can actually inspect.

Requirement: 3.2.3
Initial state: A test image that appears in no other case is prepared for upload.
Execution steps:
  1) Upload the image and note any URL assigned to it during analysis.
  2) Complete the analysis and close the session.
  3) Attempt to retrieve the noted URL from a different browser with no active session.
  4) Inspect browser storage for a retained copy of the image.
  5) Open a new session and check whether any history of the prior upload is visible.
Expected final state: The URL from step 1 is no longer retrievable by an unauthenticated
third party. No copy of the image remains in browser storage, and a new session shows no
record of the previous upload. Any retention that does occur is disclosed to the user
rather than silent.

2.8 Classification Accuracy Measurement

This case produces the accuracy, false positive, and false negative figures that
requirement 3.2.1 obliges the application to report. It is the only case that measures the
classifier's performance as a whole rather than its behavior on a single image.

Requirement: 3.2.1, 3.1.2, 3.1.4
Test data: all 54 classifiable images, subsets A through E
Initial state: The corpus is assembled locally per the manifest, and a results sheet is
prepared with one row per image.
Execution steps:
  1) Upload each of the 54 images in turn, recording the predicted label and confidence
     score against the manifest's ground truth.
  2) Tabulate the confusion matrix: true positives, true negatives, false positives, and
     false negatives, treating AI-generated as the positive class.
  3) Compute overall accuracy, false positive rate, and false negative rate.
  4) Compute the same figures separately for the high-quality subsets (A and B) and the
     degraded subsets (C and D).
Expected final state: A completed confusion matrix and the three computed rates. Accuracy
on the balanced corpus is materially above the 50% a coin flip would achieve, and the
application surfaces these figures to the user as 3.2.1 requires. The separate figures for
degraded images are expected to be worse than for high-quality images; the test passes if
that degradation is reflected in lower reported confidence rather than hidden behind
confident wrong answers.

2.9 Availability Observation Window

This case provides bounded evidence for the availability requirement. It cannot establish
a 99% annual figure and is not presented as doing so.

Requirement: 3.2.6
Initial state: The prototype is deployed and reachable at a known address.
Execution steps:
  1) Over a continuous 48-hour window, check that the application loads and returns a
     result for a fixed reference image at 30-minute intervals.
  2) Record each check as success or failure with a timestamp.
  3) Record the duration and apparent cause of any failure.
Expected final state: A log of checks across the window with no unexplained failures.
Availability over the window is computed and reported as a measurement on that window
specifically, not as a general uptime claim.

2.10 Degraded Image Edge Case

This case simulates the exploratory input typical of a classroom and the ordinary input of
a general consumer: an image that has lost forensic detail through repeated resharing.

Requirement: 3.1.2, 3.1.4, 3.1.5, 3.1.7
Test data: images from subsets C and D, paired with their originals in A and B
Initial state: The user is on the main interface.
Execution steps:
  1) Upload an original image from subset A and record the result and confidence.
  2) Upload its degraded counterpart from subset C and record the result and confidence.
  3) Repeat with a pair from subsets B and D.
Expected final state: The degraded images either classify correctly with a confidence
score visibly lower than their originals, accompanied by an explanation noting that
compression limits forensic certainty, or they trigger a graceful message stating the
image quality is insufficient for reliable analysis. A high-confidence misclassification
on a degraded image is a failure of this case. In particular, the degraded authentic image
from subset D must not be labeled AI-generated merely because compression artifacts are
present.

2.11 Ambiguous Input Handling

This case covers input where a reasonable person could disagree about the correct label,
and checks that the application expresses uncertainty rather than resolving ambiguity
arbitrarily.

Requirement: 3.1.2, 3.1.4, 3.1.5
Test data: all 8 images from subset E
Initial state: The user is on the main interface.
Execution steps:
  1) Upload each image from subset E and record the classification, confidence, and
     explanation.
  2) Compare the reported confidence against the confidence reported for clear-cut images
     in subsets A and B.
Expected final state: Confidence scores for ambiguous images are measurably lower than for
clear-cut images, and explanations acknowledge the conflicting indicators. The application
does not report high confidence on inputs that are genuinely ambiguous, such as human-made
digital artwork.

2.12 Interrupted Analysis Recovery

This case verifies that a failure during analysis is reported rather than silently
dropped. It is most relevant to consumers on unstable cellular connections.

Requirement: 3.1.7, 3.2.4
Initial state: The user is on a mobile device with a throttled connection.
Execution steps:
  1) Begin uploading a large image from subset A.
  2) Disable the network connection mid-upload.
  3) Observe the application's response.
  4) Restore the connection and retry the same upload.
Expected final state: The interruption produces a clear message that the analysis did not
complete and that no result is available, rather than an indefinite spinner or a result
produced from partial data. After the connection is restored, the retry completes normally.
