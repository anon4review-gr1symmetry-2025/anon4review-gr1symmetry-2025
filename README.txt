README file for artifact evaluation of FSE 2025 submission


"Exploiting Symmetry in GR(1) Synthesis"


===========================
1.        Artifact Contents
===========================


The zip file contains the following files and directories:


1.      tests: this directory contains tools to run the evaluation and reproduce the results

        1.1        specs: the directory of the benchmark specifications. Each Spectra specification has a token $PARAM$ that is replaced during the test with the parameter number
        1.2	   specs/mutation info: this directory contains a csv file for each benchmark specification with data on realizable/unrealizable mutations
        1.3        realizability_evaluation.jar: measures GR(1) realizability checking time
        1.4        cores_evaluation.jar: measures unrealizable/assumptions core computation time
        1.5        libcudd.so (and cudd.dll): compiled version of the CUDD library that should be present while running the jars
        
        
2.      results: this directory contains running times for the realizability checking and core computation

	2.1	   realizability checking: this directory contains CSV files that are the output of realizability_evaluation.jar, for each specification in the specs directory, for 3 versions: Orig, Sym, SymD
	2.2	   cores computation: this directory contains CSV files that are the output of cores_evaluation.jar, for each specification in the specs directory, for 3 versions: Orig, Sym, SymD
	2.3	   results_processor.jar: prints to console the output of the statistical analysis that we describe in the evaluation section in the paper
	2.4	   SymmetryEvaluationProcessor.java: the source code of the result processing JAR
	
	
3.      parametric prevalence: this directory contains data for our preliminary research question on the prevalence of parametric specification

        3.1        LTL-Synth: directory of the TLSF specs used in the SYNTCOMP competition
        3.2        ltlsynt_syntcomp_measure.sh and strix_syntcomp_measure.sh: scripts used to run both tools on the SYNTCOMP specifications
        3.3        ltlsynt_execution_times.csv and strix_execution_times.csv: tables with realizability checking runtimes for both tools
        3.4        final_execution_times.xlsx: table assembled from the results of both tools including the figures reported in the paper
        3.5        TLSF from Spectra: specifications in TLSF that were manually translated from Spectra. Used to show that GR(1) synthesis tools like Spectra perform better on similar specifications than ltlsynt and Strix, which are LTL synthesis tools


4.	FSE_2025___Supporting_Materials_for_Symmetry_for_GR_1.pdf containing additional proofs and tables

======================================
2.    Executing the evaluation JARs
======================================


0.      Make sure that your session recognizes the CUDD library by running the following command in the terminal before executing the JARs
        
        export LD_LIBRARY_PATH=[PATH_TO_LIBCUDD_SO]


        If running in Windows, make sure that the JAR is in the same directory of cudd.dll


1.      In order to run a single test (either real. check. or core comp.), run the following command in a terminal


        java -jar [realizability_evaluation.jar | cores_evaluation.jar] <PATH> <MODE> <REPEATS> <PARAMETERS>


        1.1 PATH: path of the specification with a $PARAM$ token
	1.2 MODE: has to be one of the following 3 strings, depending on the desired GR(1) algorithm:


        	1. NO_SYMMETRY                     (Orig)
        	2. SYMMETRY                        (Sym)
        	3. SYMMETRY_DISABLE_REORDER        (SymD)


        1.3 REPEATS: number of repeats for every parameter value. We set it to 10 in our experiments
        1.4 PARAMETERS: a list of parameters to replace the $PARAM$ token in the specification


Example execution:


        java -jar realizability_evaluation.jar SYMMETRY_DISABLE_REORDER specs/amba.spectra 5 8 9 10


This will run realizability checking for AMBA specification file, using the SymD algorithm, repeating 5 times every parameter value, for parameter N of sizes 8, 9, and 10


The result of a realizability checking evaluation is a CSV file in the following name format:	<NAME>_<MODE>_results.csv
The result of a cores computation evaluation is a CSV file in the following name format:	<NAME>_<MODE>_core_results.csv


The above example command will generate the file amba.spectra_SYMMETRY_DISABLE_REORDER_results.csv


===========================
3.    Results Format
===========================

The content of every csv file is a table with the measurement data.


The columns of a realizability checking csv file are:

	  3.1 Spec: path of the specification
	  3.2 Size: the value of N
	  3.3 Remove: the index of the constraint removed to obtain the mutant. The value -1 indicates the original specification
	  3.4 The time for every mutant measurement is divided into Prep. (symmetry validation and BDD construction) and Real. Check columns
	  3.5 Total: the sum of the two values in previous two columns. This is the value that we report in the paper. All values are an average of the configured number of repeats
	  

The columns of a cores computation csv file are:

	  3.1 Spec: path of the specification.
	  3.2 Size: the value of N.
	  3.3 Remove: the index of the constraint removed to obtain the mutant. The value -1 indicates the original specification
	  3.4 Is Real. if true then assumptions core is computed for the mutant, is false then unrealizability core
	  3.5 Total: Average time of the configured number of repeats
	  
	    
======================================
4.    Executing the processing JAR
======================================

In order to process the result of a single csv file / a directory containing csv files run the following command in a terminal:


        java -jar results_processor.jar <PATH_TO_RESULTS> <PATH_TO_MUTATION_INFO> <IS_CORE> [SPEC]
        
        
        1.1 PATH_TO_RESULTS: path to a directory containing csv results, either realizability checking or cores computation
        1.2 PATH_TO_MUTATION_INFO: path to a directory containing data on which mutations are realizable and which are not
	1.3 IS_CORE: true for core computation results, false for realizability check results
	1.4 SPEC: optional name of the specification file. If omitted, then the JAR prints the real/unreal division figures concerning all specifications in the directory, otherwise prints figures for a single specification
	
	
Example execution:


        java -jar results_processor.jar "results/realizability checking/" "tests/specs/mutation info/" false genbuf.spectra
        
        
This will print processed results for the realizability checking data of the GenBuf specification

