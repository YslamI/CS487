# Test Data Corpus Specification

This directory defines the image corpus used by the test cases in TEST_PLAN.md. The
image files themselves are deliberately **not** committed to this repository, for two
reasons. First, redistributing images drawn from public research datasets may conflict
with the license terms under which those datasets are released. Second, the corpus
contains personal photographs belonging to team members, which should not be published.
What is committed is the specification below and the manifest in `manifest.csv`, which
together describe exactly which images are required, where each one comes from, and what
its ground-truth label is. Any team member can reconstruct the corpus from these two
files.

## Corpus composition

The corpus contains 60 images, divided evenly between the two classes so that neither
class is favored by a trivial always-guess-one-label classifier.

| Subset | Count | Ground truth | Purpose |
|---|---|---|---|
| A. AI-generated, high quality | 15 | ai | Normal-case classification of clearly synthetic images |
| B. Authentic, high quality | 15 | real | Normal-case classification of genuine photographs |
| C. AI-generated, degraded | 8 | ai | Boundary testing: compression destroys forensic signal |
| D. Authentic, degraded | 8 | real | Boundary testing: compression must not imply "AI" |
| E. Ambiguous / adversarial | 8 | mixed | Edge cases: digital art, heavy post-processing, filters |
| F. Invalid inputs | 6 | n/a | Exceptional cases: corrupt, unsupported, empty, oversized |
| **Total** | **60** | | |

Subsets A through E total 54 classifiable images, 27 labeled `ai` and 27 labeled `real`.
Subset F contains files that are not valid images at all and are used only by the error
handling test cases.

## Sources and ground truth

Ground truth is established by provenance rather than by inspection. An image is labeled
`ai` only when a team member generated it or it came from a dataset whose images are
documented as synthetic; it is labeled `real` only when a team member photographed it or
it came from a documented photographic source. No image is labeled by guessing what it
looks like, because doing so would make the accuracy measurement circular.

- **AI-generated images (subsets A and C)** are produced by team members using publicly
  available text-to-image generators, with the generator name and date recorded in the
  manifest. Self-generation is preferred over downloading because it makes provenance
  certain.
- **Authentic images (subsets B and D)** are original photographs taken by team members
  on personal devices, retaining their original capture metadata where possible.
- **Degraded variants (subsets C and D)** are produced from subsets A and B by repeated
  re-compression and downscaling, simulating an image that has been screenshotted and
  re-shared through a social platform. The manifest records the source image each
  degraded variant was derived from.
- **Ambiguous images (subset E)** include human-made digital artwork, heavily filtered
  photographs, and photographs of screens, chosen specifically because a reasonable
  person could disagree about them.
- **Invalid inputs (subset F)** are constructed deliberately: a PDF renamed to `.jpg`, a
  JPEG truncated to half its bytes, a zero-byte file, an image exceeding the upload size
  limit, an unsupported format, and a file with a valid extension but corrupted header
  bytes.

## Class balance and measurement

Because subsets A through E are balanced at 27 `ai` and 27 `real`, the accuracy figures
computed in test case 2.8 are not skewed by class imbalance, and false positive and false
negative rates can be read directly from the confusion matrix. A classifier that labels
every image `real` would score 50% accuracy on this corpus rather than an inflated
figure, which is the property that makes the measurement meaningful.

## Manifest format

`manifest.csv` lists every image in the corpus with the following columns:

| Column | Meaning |
|---|---|
| `id` | Stable identifier, e.g. `A-01`, used to reference the image from test cases |
| `subset` | A through F, as defined above |
| `filename` | Expected filename once the corpus is assembled locally |
| `ground_truth` | `ai`, `real`, or `n/a` for invalid inputs |
| `source` | How the image was obtained (generated, photographed, derived, constructed) |
| `derived_from` | For degraded variants, the `id` of the source image |
| `notes` | Why this image is in the corpus; what it is meant to probe |

## Assembling the corpus locally

Create a directory `testdata/images/` (ignored by git) and populate it according to the
manifest. Test cases reference images by their manifest `id`, so the corpus can be
reassembled or extended without editing the test plan itself.
