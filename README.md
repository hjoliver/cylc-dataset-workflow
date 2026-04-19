# Writing a Cylc workflow to cycle over datasets

## The Problem

*We need to validate (and fix if necessary), process, and publish many datasets.
Doing so manually is time consuming and error-prone, so let's create a Cylc
workflow to automate the whole process.*


The `data` directory contains 10 datasets (and there could be many more):

```
$ tree data/
data/
├── data-0.dat
├── data-1.dat
├── data-2.dat
├── data-3.dat
├── data-4.dat  # <--- this dataset is invalid
├── data-5.dat
├── data-6.dat
├── data-7.dat
├── data-8.dat
└── data-9.dat
```

## The Applications

The `bin` directory contains 4 applications for working with these datasets:
- `validate`: check the validity of a dataset
- `autofix`: fix a common error in a dataset
- `process`: process a dataset and generate a product
- `publish`: publish a product to a location

## The Cylc Workflow Solution

We easily write a Cylc workflow that makes many applications cooperate to cycle
over many (even indefinitely many) datasets.

Cylc workflows are driven by dependencies between *tasks* that run applications.
If the depencencies represent files (one applications writes a file that another
reads, etc.) the worklow must configure the applications to use shared locations
for this file IO. Cylc provides a shared workspace in the workflow run directory
for this purpose.

The shared space is made available through several environment variables that are
automatically passed to task job scripts:

- the "share" directory: `$CYLC_WORKFLOW_SHARE_DIR`
  - e.g. `~/cylc-run/$CYLC_WORKFLOW_ID/runN/share`
- the "share/cycle" directory: `$CYLC_TASK_SHARE_CYCLE_DIR`
  - e.g. `~/cylc-run/$CYLC_WORKFLOW_ID/share/$CYLC_TASK_CYCLE_POINT`


