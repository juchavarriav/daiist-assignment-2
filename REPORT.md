# Assignment 2 Report

*Delete this italic guidance as you fill in each section. You'll be asked to
defend any of this without your code in front of you. Write only what you
can actually explain.*

- **Name**: Juan C Chavarria
- **Student ID**: 19313
- **Email**: jchavarria.ieu2022@student.ie.edu
- **Group**: [BBADBA 5B]

## Business problem, and the cost of being wrong

This model helps decide whether a camera image shows fire, so that the firemen are called in time. It would run on cameras in public buildings, and the alerts go to an operator in the building's control room. The operator looks at the image and decides whether to call the firemen.

A missed fire is the worst mistake. If a fire is found late, I assume it destroys about a quarter of a 1,000 m² Madrid building and its contents. The building is worth about €2.75 million (€2,747 per m²) and the inside about €1.1 million (€1,124 per m²), so about €3.9 million in total, and a quarter of that is roughly €1,000,000. A false alarm costs much less. If the firemen are called for nothing I assume about €1,000, and if the operator only has to look at an image I assume about €0.20. So a missed fire is worth about 1,000 false callouts, which means the model should raise alerts easily and the operator filters out the false ones.

The model has three outcomes:

| Model confidence | What happens | Cost |
|---|---|---|
| Low | Pass, nothing happens | €0 if no fire, €1,000,000 if there was a fire (missed) |
| Middle | Sent to the operator to check | about €0.20 per check |
| High | Fail, the firemen are called straight away | €0 if real fire, €1,000 if false |

Where the numbers come from:
- Building price: about €2,747 per m² for offices in Madrid in the first quarter of 2026 (Brains, https://brainsre.news/?p=302756).
- Inside of the building: about €1,124 per m² to fit out an office in Madrid in 2026 (Cushman & Wakefield fit-out guide, reported at https://brainsre.news/madrid-barcelona-competitividad-oficinas-costes/).
- Late fire damage, about a quarter of the total: my own assumption, because buildings also have smoke detectors and sprinklers, so a miss means the fire is found late, not that nothing detects it.
- False callout, €1,000: my own assumption. About €500 for six firefighters and two trucks for an hour, using the fees of a Gipuzkoa city council (€41 an hour per firefighter, €28 to €185 per vehicle, https://www.ondavasca.com/las-tasas-por-imprudencias-de-donostia-listas-para-aplicarse/amp/), plus about €500 for the building being disturbed.
- Operator check, €0.20: my own assumption, 30 seconds of an operator paid about €20 an hour including costs.
- I also assume the operator always judges correctly, which makes the review step look better than it really is.

## Dataset

I used a public dataset from Roboflow Universe, without forking it.

- Workspace: sayed-gamall
- Project: fire-smoke-detection-yolov11
- Version: 2
- Link: https://universe.roboflow.com/sayed-gamall/fire-smoke-detection-yolov11

One image is one photo. The model finds objects in it and labels them as one of two classes: Fire or Smoke. I chose it because it has both classes, and because smoke can show a fire before the flames are visible, which fits my problem of finding fires early. The photos are mostly pictures from the web (close-ups of flames, forests, smoke clouds), not frames from a security camera, so it only partly matches what the cameras in a building would see.

## What the dataset's composition implies

| Split | Images | Fire objects | Smoke objects |
|---|---|---|---|
| Train | 9,156 | 11,505 | 4,312 |
| Valid | 872 | 1,137 | 400 |
| Test | 435 | 544 | 216 |

Roboflow made 3 altered copies (flipped, rotated, brighter or darker) of every training photo. So the 9,156 training images are about 3,052 real photos. The valid and test sets are not altered.

What this means:
- There is about 2.5 times more fire than smoke, so the model has fewer examples of smoke to learn from and will probably be weaker on it. The test set has only 216 smoke objects, so the smoke results will be noisy and I should not trust small differences.
- Fire is small in the photo (about 3% of the image in the test set) and smoke is big (about 16%). Small objects are harder to find, so I expect more missed fires than missed smoke.
- Almost every image has fire or smoke in it. All 435 test images do, and only 4 of the 9,156 training images are empty. So this dataset cannot tell me how often the model raises a false alarm on a normal scene, such as an empty room. I will have to assume how often real fires happen in the cost section, and the false alarm numbers will only come from images that have smoke but no fire.
- The photos look like web photos, not security camera footage, so I cannot conclude how well the model would work on a real camera feed in a building.

## Finetuning and baselines

*Which model and task (detection or classification) and why. A results table on
the same test split: your finetuned model, the pretrained COCO model zero-shot,
and a trivial baseline. What changed, and whether the gap is as large as you
expected.*

## Per-class evaluation and failure modes

*Per-class AP50, or per-class precision/recall and the confusion matrix.
Which class carries the headline number, which one is dragging, and what the
model actually confuses with what. Show where it breaks, not just that it
works.*

## Cost matrix and operating point

*Your euro cost for each kind of error and where each figure came from. The
base rate you assumed. The cost-against-threshold sweep, and the operating
point you chose. State the assumption that would most change your answer if it
were wrong.*

## What a person still has to check

*Your two thresholds, and what share of cases they send to a person. How many
that is per day, how long one takes to check, and how many people that needs.
Is that realistic? Then your design priced against the three alternatives:
doing nothing, having a person check everything, and letting the model decide
on its own.*

## Limitations & next steps

*Real limitations you found, and concretely how you'd address each one with
more time or data. Not generic hedging.*

## Generative AI use disclosure

*Per the syllabus AI Policy: what you used and how, or "no AI content used."*
