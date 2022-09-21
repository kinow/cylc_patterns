# cylc_patterns

A collection of commonly used [Cylc](https://www.cylc.org) workflow patterns 

## Overview

This project contains a number of commonly used workflow patterns, which range from sequential to concurrent and more elaborate patterns. These patterns 
are the building blocks with which complex workflows can be assembled.

## Preliminaries

Assuming you're running on Linux or Mac OSX, you will need to have [Cylc 8](https://cylc.github.io/cylc-doc/stable/html/) installed,
```
conda create -n cylc python=3.9
conda activate cylc
conda install -c conda-forge cylc-flow
conda install -c conda-forge cylc-uiserver
```

Mac users beware, you may need to apply the fix [here](#mac-users).

## Example of a workflow pattern

Go into any of the subdirectories, e.g.
```
cd cylc-src/resilient_cycling
cylc validate .
cylc graph .
```
This shows the first three cycles of the resilient cycling pattern. The workflow graph is encoded in the `flow.cylc` file.
```
    [[graph]]
        R1 = """
            prep => check?
        """
        P1 = """
            model[-P1] => check:succeed? => model
            check:fail? => diagnose
        """
```
The first task, `prep`, creates a file, `output_file.txt`. If file `output_file.txt` is present then task `check` succeeds and task `model` is then run. Occasionally, task `model` will fail -- we allow for up to 20 attempts. When task `check` succeeds, the next cycle starts. If task `check` fails then task `diagnose` will be run. This workflow supports an infinite number of cycles.

![alt resilient cycling pattern](https://github.com/pletzer/cylc_patterns/blob/main/figures/resilient_cycling.png?raw=true)

Install the workflow with
```
cylc install resilient_cycling
```

Run the workflow with
```
cylc tui resilient_cycling
```
Type return on the workflow_name/run1 and then select "play". The figure below shows the `model` task (blue square) of the 48th cycle being run with the 49th cycle `check` waiting for the `model` task to complete. The green square indicates that 48th `check` task was successful.

![alt terminal user interface (tui) showing a cycle of the resilient cycling pattern](https://github.com/pletzer/cylc_patterns/blob/main/figures/resilient_cycling_tui.png?raw=true)


# Troubleshooting

## Mac users

If you get error
```
...
nodename nor servname provided, or not known: '1.0.0.127.in-addr.arpa'
```
or similar, then you'll have to update the `hostuserutil.py` file. Around line 113, replace
```
                target = socket.getfqdn()
```
with 
```
                target = socket.gethostname()
```




