# Empty Room, Lights On

I'm learning machine learning, and this is one of my first projects. The question: can you tell whether someone is in a room from sensor readings alone, with no camera? And if the room looks empty but the light is on, can the model warn you to switch it off?

I used the UCI Occupancy Detection dataset. The notebook downloads it, so the data is not in this repo.

Dataset: Candanedo, L. (2016). Occupancy Detection [Dataset]. UCI Machine Learning Repository. https://doi.org/10.24432/C5X01N

Page: https://archive.ics.uci.edu/dataset/357/occupancy+detection


## How I did it

The data comes in three files. I trained on one (4 to 10 Feb 2015, 8143 rows) and tested on the other two (2 to 4 Feb, 2665 rows, and 11 to 18 Feb, 9752 rows). The model never saw the test files while training. As a baseline I used "always guess empty". The model is one decision tree with max depth 3, and I did not tune anything.

## Results (accuracy)

| | Test 1 | Test 2 |
|---|---|---|
| Always guess "empty" | 0.635 | 0.790 |
| Tree, Temperature + Humidity + CO2 | 0.849 | 0.428 |
| Rule: occupied if Light > 200 | 0.979 | 0.958 |
| Tree, same features + Light | 0.978 | 0.977 |

## What happened

- Without Light, the tree did fine on test 1 but worse than the baseline on test 2. It said "occupied" far too often. In test 2 the empty room had higher CO2 and humidity than in the training data (median CO2 587.5 against 446), which might be the reason, but I did not check it, so I don't know the real cause.
- Adding Light fixed it. Light is almost everything the tree uses (importance 0.986).
- The one-line Light rule is nearly as good as the tree. On test 1 it is even slightly better. The tree only wins clearly on test 2.
- My "check the lights" idea did not work. I flag a room when the tree says "empty" and Light is above a cutoff. With a cutoff of 200 the flag was right about 12% of the time on both test files. I then tried 300, after already seeing the test results, so that choice is not clean: it was right 12.3% on test 1 and 4.2% on test 2. Some of the empty-but-lit rows may just be daylight.

## Limits

One room and a few weeks of data, so I can't say this works anywhere else. Test 1 is from before the training dates, so only test 2 is truly "later".
The occupancy labels in this dataset came from photos taken every minute, so a camera was used to create the answers, even though my model does not use one.

## Run it

Open the notebook in Google Colab and choose Runtime > Run all.


