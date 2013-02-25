Perceptron-BranchPredictor
==========================

The perceptron branch predictor implemented in Simplescalar. Performance analyzed against SPEC2000 benchmarks

Programming Assignment 2 Report (ECE 6100)
Shubhojit Chattopadhyay
GTID: 902694799
email: ssc3@gatech.edu
DYNAMIC BRANCH PREDICTORS
The aim of this assignment was to study and implement several dynamic branch predictors using
simplescalar. Sim-outorder is a performance simulator that was used for the implementation of these
branch predictors. The simulation was run on SPEC2000 benchmark programs for 200 million
instructions each. For each predictor, the effect of misprediction was studied by keeping the maximum
branch misprediction penalty 3 and 9.
The implementation for each predictor is as follows:
1) Two-bit Counter (Bimodal): The default.2bc.cfg file was configured to 'bimod' representing a
table size of 16384 entries.
2) G-share: The default.2bc.cfg file was configured to '2lev' where l1size = 1, l2size = 4096,
shift_width = 12 and XOR = 1.
3) Hybrid: The default.2bc.cfg file was configured to 'comb' representing a combination of
bimodal and 2lev predictor. The parameters for bimod was set to table size of 4096 entries and
for 2 level predictor was set to l1size = 1024, l2size = 1024, history size = 10 and XOR = 0.
The meta predictor table size was selected to be 4096.
4) Perfect: The default.2bc.cfg file was configured to 'perfect' predictor. Perfect predictors do not
need any parameters to be passed and have always 100% branch prediction rate.
5) Perceptron: A new knob was made called 'perceptron'. It needed 3 parameters to be input into
it namely, No. of entries in weight table = K = 128 or 256, Size of each weight table entry = 8
bits and size of global BHR = 27 or 54. BpredPerceptron was added to enum bpred_class. Then
in bpred_create function, that new member was presented as a case of switch statement and its
parameters from default.2bc.cfg files was read. The perceptron predictor was also allocated
BTB and RAS in this function. In bpred_dir_create function, this perceptron predictors structure
members were assigned values passed from sim-outorder through default.2bc.cfg. The weights
were all initialized to zero and the global branch history register was initialized to 1 for all bits.
In bpred_dir_lookup, the actual branch prediction was done according to the method given in
[1]. The bpred_lookup function then probed the return value of bpred_dir_lookup to predict the
branch taken or not taken. Finally, the bpred_update function was used to monitor the actual
outcome of the branch and give it a return address or branch target address. Here, the training
and update of weights for perceptron was performed based on whether the branch took the same
direction as predicted outcome or not.
Note: The configuration files mapped to the sim-outorder.c file to change the values of simulator
configuration options.Observations:
1) The IPC values in general are highest in the case of gshare and hybrid.
2) IPC values for perceptron are the least most of the times. This suggests that the use of
perceptron might lead to a reduction in the number of instructions executed per cycle.
3) The direction prediction rate seems to be the highest in the case of hybrid i.e. the misprediction
is the least. However, gshare and bimod are very close to matching hybrid.
4) It has to be noted that perceptron based direction prediction isn't the best. This can be attributed
to the fact that perceptron is a training based algorithm which may lead to many mispredictions
at the beginning and inability to learn linearly inseparable functions.
5) Floating point benchmarks have a comparatively higher direction prediction rate than integer
benchmarks
6) Increasing the misprediction penalty generally deceases the IPC for all predictors except
“Perfect” predictor.
7) “Perfect” prediction of branches yeilds highest IPC
8) Increasing the perceptron history length of the perceptron predictor increases the program IPC
and direction prediction rate. This is due to larger history length and bigger weight table size.
Conclusion:
From the IPC and direction prediction rate obtained from simulation, we can see that hybrid predictor
generally outperforms all other predictors. All the predictor mechanisms are good in their own way.
The advantage of perceptron predictor is that they can use long history lengths without requiring
exponential resources. One weakness of perceptron is its complexity as compared to bimodal and 2 l
evel branch predictors. Another weakness is their inability to learn linearly inseparable functions which
might explain the drop in direction prediction rate as compared to bimod, g-share and hybrid
predictors. It can also be observed that longer history lengths has better prediction than lower history
lengths and that increasing the misprediction penalty causes a drop in IPC.
References:
[1] Daniel A. Jiminez and Calvin Lin, Dynamic Branch Prediction with Perceptrons, Department of
Computer Sciences, The University of Texas at Austin.
The program with detailed comments is attached with this report. The IPC values and corresponding
graphs are shown below.IPC values for mplat = 3
IPC values for misprediction penalty = 3 cycles
