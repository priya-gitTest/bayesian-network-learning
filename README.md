# Bayesian Network Parameter Learning
## Course Project: COL884(Spring'18): Uncertainity in AI
### Creator: Navreet Kaur[2015TT10917]
Learning Bayesian Network parameters using Expectation-Maximisation

--> Files:
	bayesnet.py:
		classes: 	
			Graph_Node
			Network
		methods:
			read_network: Parsing the .bif format file and build a bayesian net
			markov_blanket: Get variables in the markov blanket of variable 'val_name'	
			get_data: Read data from records.dat and store as a pandas dataframe 
			normalise_counts: normalise a list of counts from a given CPT	
	utils.py:
		methods:
			setup_network
			get_missing_index: List of the indices of nodes which have missing values in each data point; equal to -1 if no value is missing
			init_params: Initialise parameters
			normalise_array: Normalise a numpy array
			get_assignment_for: return the rows of the factor table with assignments as specified in evidence E
			markov_blanket_sampling: Inference by Markov Blanket Sampling
			Expectation 
			Maximisation
			Expectation_Maximisation
			parse_output
	main.py: main file that calls methods from bayesnet and utils to build a bayes net, read data and learn its parameters


--> Objective: Bayesian Parameter Learning of Alarm Bayesian Net given data with at most one missing value in each row
--> Algorithm Used: Expectation-Maximisation

--> Assumptions:
	• All variables are missing completely(or unconditionally) at random(MCAR) and none of them are either missing at random(MAR) or missing systematically or hidden i.e. initially, probability of each missing value is the same and the sample mean of variable v is unbiased estimator of true value of v  
--> Parameter Initialisation:
	• Initialisation of parameters by available case analysis(ignoring rows with missing values if the missing value is that of the parent). Since data is MCAR, estimators based on the subsample of the data are unbiased estimators for the ones with complete data 
--> Design Choices:
	1. Data Records
		• String values for each class of random variables were mapped to integers
		• Data File was stored as a Pandas DataFrame so as to perform grouping and aggregation of certain data occurrences to get theirs counts 
	2. Network
		• Ordered dictionary to represent nodes in the graph (keys = name of random variable, value = node object)
		• Ordered dictionary to store Markov Blanket(MB) of all nodes (keys = name of random variable(X), value = list of Strings of names of nodes in MB of X) 
			◦ This is stored so as to avoid recomputation of Markov Blanket at each step while doing Markov Blanket Sampling Inference
	3. Graph_Node
		• List of Strings to store names of Parents
		• List of integers to store indices of Children in ordered dictionary of nodes in Bayes Net
		• Pandas DataFrames to store CPT
	4. CPTs
		• All CPTs are represented by Pandas DataFrames(columns are names of variables and column ‘p’ for probability value) so as to easily access the entries by specifying a dictionary of ‘Evidence’ with keys as variable names and values as the integers

--> Optimisation/Techniques:
	• Storage of only counts and not probabilities in all the CPTs and normalising them before performing Expectation step
	• Smoothing: Since all possible instances might not be observed due to small size of dataset as compared to number of network nodes, counts of all possible instances in the CPTs were set to one to initialise with. Similarly, in the Maximisation step, with any observed count was equal to zero, it was set to 0.00005 (since required precision of probabilities is upto 4 decimal places and counts in maximisation might be less than one due to weights of data points being considered, which itself lie between 0 and 1) 
	• Inference: Since the probability of variable X is independent of all other variables given its markov blanket and only one data point is missing per row(i.e. all points are given hence MB is given), therefore, P(X | data) = P(X | mb(X)), where mb(X) is the markov blanket of x. Therefore, markov blanket sampling was used to calculate P(X | MB(X))
	• Using log probabilities as addition operation is faster than multiplication and also, it helps to avoid numerical underflow.
  • Convergence Criteria: Maximum change in the CPTs in previous and current iteration is less than equal to 0.00005
