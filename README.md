# DP-MPI

This is an implementation of the distributed algorithm for dynamic programming proposed in the paper “Parallel Design Pattern for Computational Biology and Scientific Computing Applications” (W Liu, 2003). It contains code that validates the empirical results presented in the paper and can be used to solve dynamic programming tasks on a cluster with MPI.


## Distributed Dynamic Programming

Team members: Franky (1110339128), Jirka Marsik (1110339127)

Migrated to Python3 by SimonZYC

## Manifest:

- report.pdf
Our project report, the thing you are probably looking for.
- design.pdf
Our project proposal, explaining in more detail what we set out to do with this project.
- dprunner.py
The main executable Python program. This is the entry point to running the dynamic programming solver.
- sw.py, nus.py
Implementations of the parallel programming problem template for  the Smith-Waterman algorithm and Nussinov algorithm respectively.
- seqs.txt, seqs-small.txt
Input files for the benchmarks. seqs.txt contains two dummy sequences of length 3000 for the quadratic Smith-Waterman algorithm. seqs-small.txt contains a 666 character long sequence
for the cubic Nussinov algorithm. The files are simply strings of repeated As, since the content of the sequence does not influence the execution path and therefore the benchmarks.
- times.txt, times2.txt, times3.txt
Files with results from the benchmarks. times.txt contains a log of the raw measurements, while times2.txt and times3.txt are processed files created in order to produce the charts seen in the report.

## How to implement new dynamic programs:

Simply create a new Python script which defines the following three functions:
      
1. load_input(args):
This function is in charge of loading whatever the input for the program is. The parameter args is a list of command line arguments passed to the DP solver. It must return a pair whose first item can be any appropriate representation of the loaded input and whose second item must be the dimensions of the DP matrix to be used for this problem.
2. compute_cell(row, column, table, data):
The meat of the dynamic program. This function is responsible for computing the value of a cell in the DP matrix. row and column identify the position of the cell to be computed (both
zero-based). table is the DP matrix, a 2D NumPy array of doubles. data is whatever was returned as the first element from load_input.
3. write_output(args, table, data):
This function is called on the last worker and is used to wrap up the computation. It gets args, the same argument list as passed to load_input, the DP matrix with all its elements
computed (table) and the problem definition data loaded in by load_input (data).


## How to run the solver:

```
mpirun -np $procs python dprunner.py -p $program -d $div $args
```
where `$procs` is the number of processes to run, `$program` is the dynamic program to run (a path to sw.py, nus.py or another such program), `$div` is the division parameter of the block-cyclic method and `$args` are the arguments passed to load_input and write_output (in the case of sw.py and nus.py, it is a sequence of the input file and the output file).

## How we ran the benchmarks:

```
mpirun -np $procs python dprunner.py -p sw.py -d 1 data/1k.in out.txt
mpirun -np $procs python dprunner.py -p nus.py -d 1 data/sample.in out.txt
```
with `$procs` going from 1 to 16.

```
mpirun -np $procs python dprunner.py -p nus.py -d $d data/sample.in out.txt
```
with `$procs` going from 2 to 16 and `$d` going through 2, 4, 8, 16,
32 and 64.
