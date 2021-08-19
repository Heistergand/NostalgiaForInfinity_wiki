# Hyperopt values to raw values

The strategy uses hyperopt values; this has some compute overhead and thus impact runtime performance with the number of parameters available in the strategy. There is a script available to transform all hyperopt values into raw values. E.g. The following line of code:

```buy_dip_threshold_10_1 = DecimalParameter(0.001, 0.05, default=0.015, space='buy', decimals=3, optimize=False, load=True)```

Would be transformed to:

```buy_dip_threshold_10_1 = 0.015```

## Command reference
```
usage: ho_to_raw_codemod.py [-h] [--strategy STRATEGY] [--output OUTPUT_PATH]

optional arguments:
  -h, --help            show this help message and exit
  --strategy STRATEGY, -s STRATEGY
                        Name of the strategy
  --output OUTPUT_PATH, -o OUTPUT_PATH
                        Output of transformed file
```

The script has a simple CLI, where it accepts two arguments, the strategy name, and the output file,  which is the path of the transformed file. E.g.

`python codemods/ho_to_raw_codemod.py --strategy NostalgiaForInfinityNext --output NostalgiaForInfinityNext_Raw.py`
