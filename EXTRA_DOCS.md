# Using opensafely-matching

## System requirements

Requires Python 3.8+

Install with:

```
pip install opensafely-matching
```


## Input data
This is expected to be provided in two files in one of the supported formats (`.csv`, `.csv.gz` or `.arrow`) - one for the case/exposed group and one for the population to be matched.

## Use

### In a python script

Matching is run by calling the match function with at least the required arguments, as per:
```py
from osmatching import match, load_config, load_dataframe

config = load_config(
    {
        "matches_per_case": 3,
        "index_date_variable": "index_date",
        "match_variables": {
            "sex": "category",
            "age": 5
        }
    }
)
match(
    case_df=load_dataframe("input_cases.arrow"),
    match_df=load_dataframe("input_matches.arrow"),
    match_config=load_config(config)
)
```


This matches 3 matches per case, on the variables `sex`, and `age` (±5 years) and produces output files in the default `.arrow` format.\
**Outputs:**\
`output/matched_cases.arrow`\
`output/matched_matches.arrow`\
`output/matched_combined.arrow`\
`output/matching_report.txt`


### From the command line

```sh
usage: match [-h] (--config CONFIG | --config-file CONFIG) [--cases CASES]
             [--controls CONTROLS] [--output-format {arrow,csv.gz,csv}]

Matches cases to controls if provided with 2 datasets

options:
  -h, --help            show this help message and exit
  --config CONFIG       The configuration for the matching action (a JSON string)
  --config-file CONFIG  Path to the configuration JSON file for the matching action
  --cases CASES         Data file that contains the cases
  --controls CONTROLS   Data file that contains the cohort for cases
  --output-format {arrow,csv.gz,csv}
                        Format for the output files

```

To run the above example from the command line:
```sh
match --cases input_cases.arrow --controls input_matches.arrow --config-file config.json
```
where `config.json` is a file containing additional arguments to `match()`:
```
{
  "matches_per_case": 3,
  "match_variables": {
    "sex": "category",
    "age": 5
  },
  "index_date_variable": "indexdate"
}
```

Alternatively, pass config on the command line as a json string:
```sh
match \
  --cases input_cases.arrow \
  --controls input_matches.arrow \
  --config '{"matches_per_case": 3, "match_variables": {"sex": "category", "age": 5}, "index_date_variable": "indexdate"}'
```


For configuration options, please see the [reusable action documentation](README.md)
