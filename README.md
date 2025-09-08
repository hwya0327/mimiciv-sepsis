# mimiciv-sepsis: Sepsis Cohort from MIMIC-IV

This repo provides updated code for generating the sepsis cohort from the MIMIC-IV dataset based on the previous conduct of Microsoft: 

https://github.com/microsoft/mimic_sepsis

This is a pure Python implementation based on a corrected version (by the first contributor below) of the original MATLAB repo accompanying the AI Clinician paper ([Komorowski, et al](https://www.nature.com/articles/s41591-018-0213-5?sf200531662=1)) and also the Medical dead-ends paper ([Fatemi, et al](https://www.nature.com/articles/s41591-018-0213-5?sf200531662=1https://proceedings.neurips.cc/paper/2021/hash/26405399c51ad7b13b504e74eb7c696c-Abstract.html)):

https://github.com/matthieukomorowski/AI_Clinician
https://github.com/microsoft/med-deadend

### Core updates and modifications to the above repo include:

- Novel Python re-implementation
- Precise and expanded preprocess
- Clearly prevent data leakage
- Inherit the core of the previous implementation


## Contributing

- Hyunwoo Kim (hwya0327@korea.ac.kr), Integrated Ph.D. Student, University of Korea

---

## Requirements

I recommend using the [Anaconda](https://docs.anaconda.com/anaconda/install/) distribution for Python dependencies. From this standard distribution, I use both the `os` and `argparse` libraries. All other needed libraries used in this code can be found in `requirements.txt`.

## How to use

#### 1) MIMIC-IV Database
You need to first set up and configure the MIMIC-IV database. The details are provided here:

https://mimic.physionet.org/

The MIMIC database is publicly available; however, accessing MIMIC requires additional steps, which are explained at the hosting webpage.

You need to use a PostgreSQL server to manage the database (hence the use of the `psycopg2` library requirement--see `requirements.txt`). 

Other options and formats are available; see the [MIMIC repository](https://github.com/MIT-LCP/mimic-code/tree/master/buildmimic) for examples and alternatives.

After downloading and setting up the SQL files and performing all the steps from the physionet link above, you should be able to use this codebase without too much additional setup. 

#### 2) Run `preprocess.py`

This script accesses the MIMIC database and extracts sub-tables for use in defining the final septic patient cohort in the next step.

The [preamble](https://github.com/microsoft/mimic_sepsis/blob/main/preprocess.py#L17-L26) of this file will likely be the only editing needed to direct toward where a user's access to the MIMIC database is defined, as well as where they choose to save off the intermediate files.

#### 3) Run `cohort.py`

Using the sepsis-3 criteria, this script uses the preprocessed intermediate tables produced in the prior step to define a cohort of septic patients. This cohort definition was specifically designed for use in sequential decision-making purposes, yet this cohort definition code does not partition temporally spaced observations as individual data points. This script instead populates a table of patients who develop sepsis at some point during their treatment in the ICU and includes all observations 24 hours before until 48 hours after presumed onset of sepsis. Further preprocessing is required to represent this data in MDP format; an example where this is done can be found at: https://github.com/MLforHealth/rl_representations/.

The final cohort table is saved in a user-specified location in `_Z.csv` format, where the columns are z-normalized. The user can specify to also save off an unnormalized copy of the same table "_RAW.csv.

Note: The size of the cohort will depend on which version of MIMIC-IV is used. The original cohort from the 2018 Nature Medicine publication was built using MIMIC-III v1.3.

Depending on system characteristics, the full script may take 2-3 hours to run to completion.
