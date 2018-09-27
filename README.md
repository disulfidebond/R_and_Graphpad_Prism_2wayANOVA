## Common Statistical Tests in R and and Graphpad® Prism: 2way-ANOVA

### Synopsis:

ANOVA is short for ANalysis Of VAriance.  A two-way ANOVA expands on a [one-way ANOVA](https://dholk.primate.wisc.edu/project/dho/experiments/21507/begin.view?) by contrasting row values *and* column values..  It is [primarily designed to work with normal data](https://stats.idre.ucla.edu/other/mult-pkg/whatstat/what-is-the-difference-between-categorical-ordinal-and-interval-variables/).  If you think you may be using the wrong test, [see this useful reference](https://stats.idre.ucla.edu/other/mult-pkg/whatstat/), or the attached PDF "Categorical_Data.PDF".

Due to a "quirk" in how R operates, this is divided into seven sections: The two-way ANOVA in GraphPad Prism, two-way ANOVA in R, Multiple Hypothesis testing in GraphPad Prism (four are listed), and finally, Multiple Hypothesis Testing in R.


#### Example Data: Samples A,B,C with expression values for gene1,gene2,gene3,gene4,gene5
      
        # SampleData
        GeneID,A,A,B,B,C,C
        gene1,1,3,4,2,4,5
        gene2,2,4,2,1,6,2
        gene3,2,3,2,2,1,4
        gene4,4,2,1,1,2,3
        gene5,2,3,4,3,5,4
        

### Two-Way ANOVA using Graphpad® Prism
1) Open Graphpad Prism.  Select Grouped, and then select "Enter Replicate Values into side-by-side columns"
2) Use the stepper, or manually enter "2" in the above selection for the number of replicates.
2) Copy and paste, or enter the values manually into the spreadsheet that opens, making sure to line up the replicates and the gene names.
3) Click "Analyze", then select "Two-Way Anova"
4) Select the options:
* Under Experimental Design, select "No Matching", if it is not already selected.
* Under Experimental Design, label the factor names that defines columns as "Sample", and the factor name that defines rows as "Genes".
5) You should see this as the output:
        
                "Table Analyzed"	"Data 1"				
        					
        "Two-way ANOVA"	Ordinary				
        Alpha	0.05				
        					
        "Source of Variation"	"% of total variation"	"P value"	"P value summary"	Significant?	
        "  Interaction"	20.71	0.5727	ns	No	
        "  Genes"	14.14	0.3645	ns	No	
        "  Sample"	19.7	0.0672	ns	No	
        					
        "ANOVA table"	SS	DF	MS	"F (DFn, DFd)"	"P value"
        "  Interaction"	10.93	8	1.367	"F (8, 15) = 0.8542"	P=0.5727
        "  Genes"	7.467	4	1.867	"F (4, 15) = 1.167"	P=0.3645
        "  Sample"	10.4	2	5.2	"F (2, 15) = 3.25"	P=0.0672
        "  Residual"	24	15	1.6		
        					
        "Number of missing values"	0


### Two-Way ANOVA using R
R can do a lot of the analysis for you, but you *must* format the data properly.  This can either be done within R, in Excel, or by manually entering the values.

To run an ANOVA in R, you must provide R the values as well as the grouping (in this case, the factor) for the values.
This means this data:

        # Sample Data from above
        GeneID,A,A,B,B,C,C
        gene1,1,3,4,2,4,5
        gene2,2,4,2,1,6,2
        gene3,2,3,2,2,1,4
        gene4,4,2,1,1,2,3
        gene5,2,3,4,3,5,4

Must be formatted like this, if you wish you can assign the column names of 'Sample' and 'Value' as headers:

        Groups Values GeneNames
         A      1     gene1
         A      2     gene2
         A      2     gene3
         A      4     gene4
         A      2     gene5
         A      3     gene1
         A      4     gene2
         A      3     gene3
         A      2     gene4
        A      3     gene5
        B      4     gene1
        B      2     gene2
        B      2     gene3
        B      1     gene4
        B      4     gene5
        B      2     gene1
        B      1     gene2
        B      2     gene3
        B      1     gene4
        B      3     gene5
        C      4     gene1
        C      6     gene2
        C      1     gene3
        C      2     gene4
        C      5     gene5
        C      5     gene1
        C      2     gene2
        C      4     gene3
        C      3     gene4
        C      4     gene5


There are several ways to do this in R; here is one possibility using the attached file "dataset4R.csv"

        rData <- reac.csv('dataset4R-ANOVA2.csv')
        # format the dataframe by using the data.frame() command to create a new dataframe from the old one
        # the syntax is: data.frame(firstColumnValue=c(columnNames...), secondColumnValue=cbind(c(column1, column2, column3)))
        anovaDF <- data.frame(Groups=c(rep("A", 10), rep("B", 10), rep("C", 10)),Values=cbind(c(rData[,"A"], rData[,"A.1"], rData[,"B"], rData[,"B.1"], rData[,"C"], rData[,"C.1"])), Genes=c(rep(c("gene1", "gene2", "gene3", "gene4", "gene5"), 3)))

        # Then do Ordinary Two-Way ANOVA
        # The default behavior in R is to compare each mean with every other cell
        # and compare one family per column
        # This is equivalent in Graphpad© Prism© to the settings above for Ordinary Two-Way ANOVA.
        # # Ordinary Two-Way ANOVA
        # # Within each column, compare the mean of side by side replicates of one row with the mean of other rows
        anovaResults <- aov(formula = Values ~ Genes * Group, data = anovaDF)

        # display Anova results
        # Comparing these results to Graphpad Prism above, Group == Column, Genes = Rows, Group:Genes == interaction
        > summary(anovaResults)
                    Df Sum Sq Mean Sq F value Pr(>F)  
        Genes        4  7.467   1.867   1.167 0.3645  
        Group        2 10.400   5.200   3.250 0.0672 .
        Genes:Group  8 10.933   1.367   0.854 0.5727  
        Residuals   15 24.000   1.600                 
        ---
        Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1


### Multiple Comparisons
#### To conduct a multiple comparison and multiple hypothesis testing to compare Row Means (Main Row effect):
* In Graphpad Prism, repeat the steps above for Two-Way ANOVA, but Under Multiple Comparisons, select the option "Compare Row Means (main row effect)"
* Under Options, select Correct for multiple comparisons using statistical hypothesis testing, test: Tukey.  Leave all other values as the defaults.

You will see this output for Multiple comparisons:

        "Compare row means (main row effect)"								
        								
        "Number of families"	1							
        "Number of comparisons per family"	10							
        Alpha	0.05							
        								
        "Tukey's multiple comparisons test"	"Mean Diff."	"95.00% CI of diff."	Significant?	Summary	"Adjusted P Value"			
        								
        "  gene1 vs. gene2"	0.3333	"-1.922 to 2.588"	No	ns	0.9901			
        "  gene1 vs. gene3"	0.8333	"-1.422 to 3.088"	No	ns	0.7829			
        "  gene1 vs. gene4"	1	"-1.255 to 3.255"	No	ns	0.6549			
        "  gene1 vs. gene5"	-0.3333	"-2.588 to 1.922"	No	ns	0.9901			
        "  gene2 vs. gene3"	0.5	"-1.755 to 2.755"	No	ns	0.9568			
        "  gene2 vs. gene4"	0.6667	"-1.588 to 2.922"	No	ns	0.8876			
        "  gene2 vs. gene5"	-0.6667	"-2.922 to 1.588"	No	ns	0.8876			
        "  gene3 vs. gene4"	0.1667	"-2.088 to 2.422"	No	ns	0.9993			
        "  gene3 vs. gene5"	-1.167	"-3.422 to 1.088"	No	ns	0.5208			
        "  gene4 vs. gene5"	-1.333	"-3.588 to 0.9218"	No	ns	0.3956			
        								
        								
        "Test details"	"Mean 1"	"Mean 2"	"Mean Diff."	"SE of diff."	N1	N2	q	DF
        								
        "  gene1 vs. gene2"	3.167	2.833	0.3333	0.7303	6	6	0.6455	15
        "  gene1 vs. gene3"	3.167	2.333	0.8333	0.7303	6	6	1.614	15
        "  gene1 vs. gene4"	3.167	2.167	1	0.7303	6	6	1.936	15
        "  gene1 vs. gene5"	3.167	3.5	-0.3333	0.7303	6	6	0.6455	15
        "  gene2 vs. gene3"	2.833	2.333	0.5	0.7303	6	6	0.9682	15
        "  gene2 vs. gene4"	2.833	2.167	0.6667	0.7303	6	6	1.291	15
        "  gene2 vs. gene5"	2.833	3.5	-0.6667	0.7303	6	6	1.291	15
        "  gene3 vs. gene4"	2.333	2.167	0.1667	0.7303	6	6	0.3227	15
        "  gene3 vs. gene5"	2.333	3.5	-1.167	0.7303	6	6	2.259	15
        "  gene4 vs. gene5"	2.167	3.5	-1.333	0.7303	6	6	2.582	15

* To see how do this in R, skip to the last section.


#### To conduct a multiple comparison and multiple hypothesis testing to compare column means (main column effect):
* In Graphpad Prism, repeat the steps above for Two-Way ANOVA, but Under Multiple Comparisons, select the option "Compare column means (main column effect)"
* Under Options, select Correct for multiple comparisons using statistical hypothesis testing, test: Tukey.  Leave all other values as the defaults.
You will see this output for Multiple comparisons:

        "Compare column means (main column effect)"								
        								
        "Number of families"	1							
        "Number of comparisons per family"	3							
        Alpha	0.05							
        								
        "Tukey's multiple comparisons test"	"Mean Diff."	"95.00% CI of diff."	Significant?	Summary	"Adjusted P Value"			
        								
        "  A vs. B"	0.4	"-1.069 to 1.869"	No	ns	0.7631			
        "  A vs. C"	-1	"-2.469 to 0.4694"	No	ns	0.2138			
        "  B vs. C"	-1.4	"-2.869 to 0.06935"	No	ns	0.0629			
        								
        								
        "Test details"	"Mean 1"	"Mean 2"	"Mean Diff."	"SE of diff."	N1	N2	q	DF
        								
        "  A vs. B"	2.6	2.2	0.4	0.5657	10	10	1	15
        "  A vs. C"	2.6	3.6	-1	0.5657	10	10	2.5	15
        "  B vs. C"	2.2	3.6	-1.4	0.5657	10	10	3.5	15

* To see how do this in R, skip to the last section.

#### To conduct a multiple comparison and multiple hypothesis testing for Within each column, compare rows (simple effects within columns):
* In Graphpad Prism, repeat the steps above for Two-Way ANOVA, but Under Multiple Comparisons, select the option "Within each column, compare rows (simple effects within columns)"
* Under Options, select Correct for multiple comparisons using statistical hypothesis testing, test: Tukey.  Leave all other values as the defaults.
You will see this output for Multiple comparisons:

        "Within each column, compare rows (simple effects within columns)"								
        								
        "Number of families"	3							
        "Number of comparisons per family"	10							
        Alpha	0.05							
        								
        "Tukey's multiple comparisons test"	"Mean Diff."	"95.00% CI of diff."	Significant?	Summary	"Adjusted P Value"			
        								
        "  A"								
        "    gene2 vs. gene1"	1	"-2.906 to 4.906"	No	ns	0.9294			
        "    gene3 vs. gene1"	0.5	"-3.406 to 4.406"	No	ns	0.9943			
        "    gene4 vs. gene1"	1	"-2.906 to 4.906"	No	ns	0.9294			
        "    gene5 vs. gene1"	0.5	"-3.406 to 4.406"	No	ns	0.9943			
        "    gene3 vs. gene2"	-0.5	"-4.406 to 3.406"	No	ns	0.9943			
        "    gene4 vs. gene2"	0	"-3.906 to 3.906"	No	ns	>0.9999			
        "    gene5 vs. gene2"	-0.5	"-4.406 to 3.406"	No	ns	0.9943			
        "    gene4 vs. gene3"	0.5	"-3.406 to 4.406"	No	ns	0.9943			
        "    gene5 vs. gene3"	0	"-3.906 to 3.906"	No	ns	>0.9999			
        "    gene5 vs. gene4"	-0.5	"-4.406 to 3.406"	No	ns	0.9943			
        								
        "  B"								
        "    gene2 vs. gene1"	-1.5	"-5.406 to 2.406"	No	ns	0.7591			
        "    gene3 vs. gene1"	-1	"-4.906 to 2.906"	No	ns	0.9294			
        "    gene4 vs. gene1"	-2	"-5.906 to 1.906"	No	ns	0.5303			
        "    gene5 vs. gene1"	0.5	"-3.406 to 4.406"	No	ns	0.9943			
        "    gene3 vs. gene2"	0.5	"-3.406 to 4.406"	No	ns	0.9943			
        "    gene4 vs. gene2"	-0.5	"-4.406 to 3.406"	No	ns	0.9943			
        "    gene5 vs. gene2"	2	"-1.906 to 5.906"	No	ns	0.5303			
        "    gene4 vs. gene3"	-1	"-4.906 to 2.906"	No	ns	0.9294			
        "    gene5 vs. gene3"	1.5	"-2.406 to 5.406"	No	ns	0.7591			
        "    gene5 vs. gene4"	2.5	"-1.406 to 6.406"	No	ns	0.3227			
        								
        "  C"								
        "    gene2 vs. gene1"	-0.5	"-4.406 to 3.406"	No	ns	0.9943			
        "    gene3 vs. gene1"	-2	"-5.906 to 1.906"	No	ns	0.5303			
        "    gene4 vs. gene1"	-2	"-5.906 to 1.906"	No	ns	0.5303			
        "    gene5 vs. gene1"	0	"-3.906 to 3.906"	No	ns	>0.9999			
        "    gene3 vs. gene2"	-1.5	"-5.406 to 2.406"	No	ns	0.7591			
        "    gene4 vs. gene2"	-1.5	"-5.406 to 2.406"	No	ns	0.7591			
        "    gene5 vs. gene2"	0.5	"-3.406 to 4.406"	No	ns	0.9943			
        "    gene4 vs. gene3"	0	"-3.906 to 3.906"	No	ns	>0.9999			
        "    gene5 vs. gene3"	2	"-1.906 to 5.906"	No	ns	0.5303			
        "    gene5 vs. gene4"	2	"-1.906 to 5.906"	No	ns	0.5303			
        								
        								
        "Test details"	"Mean 1"	"Mean 2"	"Mean Diff."	"SE of diff."	N1	N2	q	DF
        								
        "  A"								
        "    gene2 vs. gene1"	3	2	1	1.265	2	2	1.118	15
        "    gene3 vs. gene1"	2.5	2	0.5	1.265	2	2	0.559	15
        "    gene4 vs. gene1"	3	2	1	1.265	2	2	1.118	15
        "    gene5 vs. gene1"	2.5	2	0.5	1.265	2	2	0.559	15
        "    gene3 vs. gene2"	2.5	3	-0.5	1.265	2	2	0.559	15
        "    gene4 vs. gene2"	3	3	0	1.265	2	2	0	15
        "    gene5 vs. gene2"	2.5	3	-0.5	1.265	2	2	0.559	15
        "    gene4 vs. gene3"	3	2.5	0.5	1.265	2	2	0.559	15
        "    gene5 vs. gene3"	2.5	2.5	0	1.265	2	2	0	15
        "    gene5 vs. gene4"	2.5	3	-0.5	1.265	2	2	0.559	15
        								
        "  B"								
        "    gene2 vs. gene1"	1.5	3	-1.5	1.265	2	2	1.677	15
        "    gene3 vs. gene1"	2	3	-1	1.265	2	2	1.118	15
        "    gene4 vs. gene1"	1	3	-2	1.265	2	2	2.236	15
        "    gene5 vs. gene1"	3.5	3	0.5	1.265	2	2	0.559	15
        "    gene3 vs. gene2"	2	1.5	0.5	1.265	2	2	0.559	15
        "    gene4 vs. gene2"	1	1.5	-0.5	1.265	2	2	0.559	15
        "    gene5 vs. gene2"	3.5	1.5	2	1.265	2	2	2.236	15
        "    gene4 vs. gene3"	1	2	-1	1.265	2	2	1.118	15
        "    gene5 vs. gene3"	3.5	2	1.5	1.265	2	2	1.677	15
        "    gene5 vs. gene4"	3.5	1	2.5	1.265	2	2	2.795	15
        								
        "  C"								
        "    gene2 vs. gene1"	4	4.5	-0.5	1.265	2	2	0.559	15
        "    gene3 vs. gene1"	2.5	4.5	-2	1.265	2	2	2.236	15
        "    gene4 vs. gene1"	2.5	4.5	-2	1.265	2	2	2.236	15
        "    gene5 vs. gene1"	4.5	4.5	0	1.265	2	2	0	15
        "    gene3 vs. gene2"	2.5	4	-1.5	1.265	2	2	1.677	15
        "    gene4 vs. gene2"	2.5	4	-1.5	1.265	2	2	1.677	15
        "    gene5 vs. gene2"	4.5	4	0.5	1.265	2	2	0.559	15
        "    gene4 vs. gene3"	2.5	2.5	0	1.265	2	2	0	15
        "    gene5 vs. gene3"	4.5	2.5	2	1.265	2	2	2.236	15
        "    gene5 vs. gene4"	4.5	2.5	2	1.265	2	2	2.236	15

* This cannot be done easily within R; skip to the last section and see "Caveat to NOTE1".

#### To conduct a multiple comparison and multiple hypothesis testing for Compare cell means regardless of rows and columns:
* In Graphpad Prism, repeat the steps above for Two-Way ANOVA, but Under Multiple Comparisons, select the option "Compare cell means regardless of rows and columns"
* Under Options, select Correct for multiple comparisons using statistical hypothesis testing, test: Tukey.  Leave all other values as the defaults.
You will see this output for Multiple comparisons:

        "Compare cell means regardless of rows and columns"								
        								
        "Number of families"	1							
        "Number of comparisons per family"	105							
        Alpha	0.05							
        								
        "Tukey's multiple comparisons test"	"Mean Diff."	"95.00% CI of diff."	Significant?	Summary	"Adjusted P Value"			
        								
        "  gene1:A vs. gene1:B"	-1	"-6.053 to 4.053"	No	ns	0.9999			
        "  gene1:A vs. gene1:C"	-2.5	"-7.553 to 2.553"	No	ns	0.7847			
        "  gene1:A vs. gene2:A"	-1	"-6.053 to 4.053"	No	ns	0.9999			
        "  gene1:A vs. gene2:B"	0.5	"-4.553 to 5.553"	No	ns	>0.9999			
        "  gene1:A vs. gene2:C"	-2	"-7.053 to 3.053"	No	ns	0.9391			
        "  gene1:A vs. gene3:A"	-0.5	"-5.553 to 4.553"	No	ns	>0.9999			
        "  gene1:A vs. gene3:B"	0	"-5.053 to 5.053"	No	ns	>0.9999			
        "  gene1:A vs. gene3:C"	-0.5	"-5.553 to 4.553"	No	ns	>0.9999			
        "  gene1:A vs. gene4:A"	-1	"-6.053 to 4.053"	No	ns	0.9999			
        "  gene1:A vs. gene4:B"	1	"-4.053 to 6.053"	No	ns	0.9999			
        "  gene1:A vs. gene4:C"	-0.5	"-5.553 to 4.553"	No	ns	>0.9999			
        "  gene1:A vs. gene5:A"	-0.5	"-5.553 to 4.553"	No	ns	>0.9999			
        "  gene1:A vs. gene5:B"	-1.5	"-6.553 to 3.553"	No	ns	0.9936			
        "  gene1:A vs. gene5:C"	-2.5	"-7.553 to 2.553"	No	ns	0.7847			
        "  gene1:B vs. gene1:C"	-1.5	"-6.553 to 3.553"	No	ns	0.9936			
        "  gene1:B vs. gene2:A"	0	"-5.053 to 5.053"	No	ns	>0.9999			
        "  gene1:B vs. gene2:B"	1.5	"-3.553 to 6.553"	No	ns	0.9936			
        "  gene1:B vs. gene2:C"	-1	"-6.053 to 4.053"	No	ns	0.9999			
        "  gene1:B vs. gene3:A"	0.5	"-4.553 to 5.553"	No	ns	>0.9999			
        "  gene1:B vs. gene3:B"	1	"-4.053 to 6.053"	No	ns	0.9999			
        "  gene1:B vs. gene3:C"	0.5	"-4.553 to 5.553"	No	ns	>0.9999			
        "  gene1:B vs. gene4:A"	0	"-5.053 to 5.053"	No	ns	>0.9999			
        "  gene1:B vs. gene4:B"	2	"-3.053 to 7.053"	No	ns	0.9391			
        "  gene1:B vs. gene4:C"	0.5	"-4.553 to 5.553"	No	ns	>0.9999			
        "  gene1:B vs. gene5:A"	0.5	"-4.553 to 5.553"	No	ns	>0.9999			
        "  gene1:B vs. gene5:B"	-0.5	"-5.553 to 4.553"	No	ns	>0.9999			
        "  gene1:B vs. gene5:C"	-1.5	"-6.553 to 3.553"	No	ns	0.9936			
        "  gene1:C vs. gene2:A"	1.5	"-3.553 to 6.553"	No	ns	0.9936			
        "  gene1:C vs. gene2:B"	3	"-2.053 to 8.053"	No	ns	0.5625			
        "  gene1:C vs. gene2:C"	0.5	"-4.553 to 5.553"	No	ns	>0.9999			
        "  gene1:C vs. gene3:A"	2	"-3.053 to 7.053"	No	ns	0.9391			
        "  gene1:C vs. gene3:B"	2.5	"-2.553 to 7.553"	No	ns	0.7847			
        "  gene1:C vs. gene3:C"	2	"-3.053 to 7.053"	No	ns	0.9391			
        "  gene1:C vs. gene4:A"	1.5	"-3.553 to 6.553"	No	ns	0.9936			
        "  gene1:C vs. gene4:B"	3.5	"-1.553 to 8.553"	No	ns	0.3523			
        "  gene1:C vs. gene4:C"	2	"-3.053 to 7.053"	No	ns	0.9391			
        "  gene1:C vs. gene5:A"	2	"-3.053 to 7.053"	No	ns	0.9391			
        "  gene1:C vs. gene5:B"	1	"-4.053 to 6.053"	No	ns	0.9999			
        "  gene1:C vs. gene5:C"	0	"-5.053 to 5.053"	No	ns	>0.9999			
        "  gene2:A vs. gene2:B"	1.5	"-3.553 to 6.553"	No	ns	0.9936			
        "  gene2:A vs. gene2:C"	-1	"-6.053 to 4.053"	No	ns	0.9999			
        "  gene2:A vs. gene3:A"	0.5	"-4.553 to 5.553"	No	ns	>0.9999			
        "  gene2:A vs. gene3:B"	1	"-4.053 to 6.053"	No	ns	0.9999			
        "  gene2:A vs. gene3:C"	0.5	"-4.553 to 5.553"	No	ns	>0.9999			
        "  gene2:A vs. gene4:A"	0	"-5.053 to 5.053"	No	ns	>0.9999			
        "  gene2:A vs. gene4:B"	2	"-3.053 to 7.053"	No	ns	0.9391			
        "  gene2:A vs. gene4:C"	0.5	"-4.553 to 5.553"	No	ns	>0.9999			
        "  gene2:A vs. gene5:A"	0.5	"-4.553 to 5.553"	No	ns	>0.9999			
        "  gene2:A vs. gene5:B"	-0.5	"-5.553 to 4.553"	No	ns	>0.9999			
        "  gene2:A vs. gene5:C"	-1.5	"-6.553 to 3.553"	No	ns	0.9936			
        "  gene2:B vs. gene2:C"	-2.5	"-7.553 to 2.553"	No	ns	0.7847			
        "  gene2:B vs. gene3:A"	-1	"-6.053 to 4.053"	No	ns	0.9999			
        "  gene2:B vs. gene3:B"	-0.5	"-5.553 to 4.553"	No	ns	>0.9999			
        "  gene2:B vs. gene3:C"	-1	"-6.053 to 4.053"	No	ns	0.9999			
        "  gene2:B vs. gene4:A"	-1.5	"-6.553 to 3.553"	No	ns	0.9936			
        "  gene2:B vs. gene4:B"	0.5	"-4.553 to 5.553"	No	ns	>0.9999			
        "  gene2:B vs. gene4:C"	-1	"-6.053 to 4.053"	No	ns	0.9999			
        "  gene2:B vs. gene5:A"	-1	"-6.053 to 4.053"	No	ns	0.9999			
        "  gene2:B vs. gene5:B"	-2	"-7.053 to 3.053"	No	ns	0.9391			
        "  gene2:B vs. gene5:C"	-3	"-8.053 to 2.053"	No	ns	0.5625			
        "  gene2:C vs. gene3:A"	1.5	"-3.553 to 6.553"	No	ns	0.9936			
        "  gene2:C vs. gene3:B"	2	"-3.053 to 7.053"	No	ns	0.9391			
        "  gene2:C vs. gene3:C"	1.5	"-3.553 to 6.553"	No	ns	0.9936			
        "  gene2:C vs. gene4:A"	1	"-4.053 to 6.053"	No	ns	0.9999			
        "  gene2:C vs. gene4:B"	3	"-2.053 to 8.053"	No	ns	0.5625			
        "  gene2:C vs. gene4:C"	1.5	"-3.553 to 6.553"	No	ns	0.9936			
        "  gene2:C vs. gene5:A"	1.5	"-3.553 to 6.553"	No	ns	0.9936			
        "  gene2:C vs. gene5:B"	0.5	"-4.553 to 5.553"	No	ns	>0.9999			
        "  gene2:C vs. gene5:C"	-0.5	"-5.553 to 4.553"	No	ns	>0.9999			
        "  gene3:A vs. gene3:B"	0.5	"-4.553 to 5.553"	No	ns	>0.9999			
        "  gene3:A vs. gene3:C"	0	"-5.053 to 5.053"	No	ns	>0.9999			
        "  gene3:A vs. gene4:A"	-0.5	"-5.553 to 4.553"	No	ns	>0.9999			
        "  gene3:A vs. gene4:B"	1.5	"-3.553 to 6.553"	No	ns	0.9936			
        "  gene3:A vs. gene4:C"	0	"-5.053 to 5.053"	No	ns	>0.9999			
        "  gene3:A vs. gene5:A"	0	"-5.053 to 5.053"	No	ns	>0.9999			
        "  gene3:A vs. gene5:B"	-1	"-6.053 to 4.053"	No	ns	0.9999			
        "  gene3:A vs. gene5:C"	-2	"-7.053 to 3.053"	No	ns	0.9391			
        "  gene3:B vs. gene3:C"	-0.5	"-5.553 to 4.553"	No	ns	>0.9999			
        "  gene3:B vs. gene4:A"	-1	"-6.053 to 4.053"	No	ns	0.9999			
        "  gene3:B vs. gene4:B"	1	"-4.053 to 6.053"	No	ns	0.9999			
        "  gene3:B vs. gene4:C"	-0.5	"-5.553 to 4.553"	No	ns	>0.9999			
        "  gene3:B vs. gene5:A"	-0.5	"-5.553 to 4.553"	No	ns	>0.9999			
        "  gene3:B vs. gene5:B"	-1.5	"-6.553 to 3.553"	No	ns	0.9936			
        "  gene3:B vs. gene5:C"	-2.5	"-7.553 to 2.553"	No	ns	0.7847			
        "  gene3:C vs. gene4:A"	-0.5	"-5.553 to 4.553"	No	ns	>0.9999			
        "  gene3:C vs. gene4:B"	1.5	"-3.553 to 6.553"	No	ns	0.9936			
        "  gene3:C vs. gene4:C"	0	"-5.053 to 5.053"	No	ns	>0.9999			
        "  gene3:C vs. gene5:A"	0	"-5.053 to 5.053"	No	ns	>0.9999			
        "  gene3:C vs. gene5:B"	-1	"-6.053 to 4.053"	No	ns	0.9999			
        "  gene3:C vs. gene5:C"	-2	"-7.053 to 3.053"	No	ns	0.9391			
        "  gene4:A vs. gene4:B"	2	"-3.053 to 7.053"	No	ns	0.9391			
        "  gene4:A vs. gene4:C"	0.5	"-4.553 to 5.553"	No	ns	>0.9999			
        "  gene4:A vs. gene5:A"	0.5	"-4.553 to 5.553"	No	ns	>0.9999			
        "  gene4:A vs. gene5:B"	-0.5	"-5.553 to 4.553"	No	ns	>0.9999			
        "  gene4:A vs. gene5:C"	-1.5	"-6.553 to 3.553"	No	ns	0.9936			
        "  gene4:B vs. gene4:C"	-1.5	"-6.553 to 3.553"	No	ns	0.9936			
        "  gene4:B vs. gene5:A"	-1.5	"-6.553 to 3.553"	No	ns	0.9936			
        "  gene4:B vs. gene5:B"	-2.5	"-7.553 to 2.553"	No	ns	0.7847			
        "  gene4:B vs. gene5:C"	-3.5	"-8.553 to 1.553"	No	ns	0.3523			
        "  gene4:C vs. gene5:A"	0	"-5.053 to 5.053"	No	ns	>0.9999			
        "  gene4:C vs. gene5:B"	-1	"-6.053 to 4.053"	No	ns	0.9999			
        "  gene4:C vs. gene5:C"	-2	"-7.053 to 3.053"	No	ns	0.9391			
        "  gene5:A vs. gene5:B"	-1	"-6.053 to 4.053"	No	ns	0.9999			
        "  gene5:A vs. gene5:C"	-2	"-7.053 to 3.053"	No	ns	0.9391			
        "  gene5:B vs. gene5:C"	-1	"-6.053 to 4.053"	No	ns	0.9999			
        								
        								
        "Test details"	"Mean 1"	"Mean 2"	"Mean Diff."	"SE of diff."	N1	N2	q	DF
        								
        "  gene1:A vs. gene1:B"	2	3	-1	1.265	2	2	1.118	15
        "  gene1:A vs. gene1:C"	2	4.5	-2.5	1.265	2	2	2.795	15
        "  gene1:A vs. gene2:A"	2	3	-1	1.265	2	2	1.118	15
        "  gene1:A vs. gene2:B"	2	1.5	0.5	1.265	2	2	0.559	15
        "  gene1:A vs. gene2:C"	2	4	-2	1.265	2	2	2.236	15
        "  gene1:A vs. gene3:A"	2	2.5	-0.5	1.265	2	2	0.559	15
        "  gene1:A vs. gene3:B"	2	2	0	1.265	2	2	0	15
        "  gene1:A vs. gene3:C"	2	2.5	-0.5	1.265	2	2	0.559	15
        "  gene1:A vs. gene4:A"	2	3	-1	1.265	2	2	1.118	15
        "  gene1:A vs. gene4:B"	2	1	1	1.265	2	2	1.118	15
        "  gene1:A vs. gene4:C"	2	2.5	-0.5	1.265	2	2	0.559	15
        "  gene1:A vs. gene5:A"	2	2.5	-0.5	1.265	2	2	0.559	15
        "  gene1:A vs. gene5:B"	2	3.5	-1.5	1.265	2	2	1.677	15
        "  gene1:A vs. gene5:C"	2	4.5	-2.5	1.265	2	2	2.795	15
        "  gene1:B vs. gene1:C"	3	4.5	-1.5	1.265	2	2	1.677	15
        "  gene1:B vs. gene2:A"	3	3	0	1.265	2	2	0	15
        "  gene1:B vs. gene2:B"	3	1.5	1.5	1.265	2	2	1.677	15
        "  gene1:B vs. gene2:C"	3	4	-1	1.265	2	2	1.118	15
        "  gene1:B vs. gene3:A"	3	2.5	0.5	1.265	2	2	0.559	15
        "  gene1:B vs. gene3:B"	3	2	1	1.265	2	2	1.118	15
        "  gene1:B vs. gene3:C"	3	2.5	0.5	1.265	2	2	0.559	15
        "  gene1:B vs. gene4:A"	3	3	0	1.265	2	2	0	15
        "  gene1:B vs. gene4:B"	3	1	2	1.265	2	2	2.236	15
        "  gene1:B vs. gene4:C"	3	2.5	0.5	1.265	2	2	0.559	15
        "  gene1:B vs. gene5:A"	3	2.5	0.5	1.265	2	2	0.559	15
        "  gene1:B vs. gene5:B"	3	3.5	-0.5	1.265	2	2	0.559	15
        "  gene1:B vs. gene5:C"	3	4.5	-1.5	1.265	2	2	1.677	15
        "  gene1:C vs. gene2:A"	4.5	3	1.5	1.265	2	2	1.677	15
        "  gene1:C vs. gene2:B"	4.5	1.5	3	1.265	2	2	3.354	15
        "  gene1:C vs. gene2:C"	4.5	4	0.5	1.265	2	2	0.559	15
        "  gene1:C vs. gene3:A"	4.5	2.5	2	1.265	2	2	2.236	15
        "  gene1:C vs. gene3:B"	4.5	2	2.5	1.265	2	2	2.795	15
        "  gene1:C vs. gene3:C"	4.5	2.5	2	1.265	2	2	2.236	15
        "  gene1:C vs. gene4:A"	4.5	3	1.5	1.265	2	2	1.677	15
        "  gene1:C vs. gene4:B"	4.5	1	3.5	1.265	2	2	3.913	15
        "  gene1:C vs. gene4:C"	4.5	2.5	2	1.265	2	2	2.236	15
        "  gene1:C vs. gene5:A"	4.5	2.5	2	1.265	2	2	2.236	15
        "  gene1:C vs. gene5:B"	4.5	3.5	1	1.265	2	2	1.118	15
        "  gene1:C vs. gene5:C"	4.5	4.5	0	1.265	2	2	0	15
        "  gene2:A vs. gene2:B"	3	1.5	1.5	1.265	2	2	1.677	15
        "  gene2:A vs. gene2:C"	3	4	-1	1.265	2	2	1.118	15
        "  gene2:A vs. gene3:A"	3	2.5	0.5	1.265	2	2	0.559	15
        "  gene2:A vs. gene3:B"	3	2	1	1.265	2	2	1.118	15
        "  gene2:A vs. gene3:C"	3	2.5	0.5	1.265	2	2	0.559	15
        "  gene2:A vs. gene4:A"	3	3	0	1.265	2	2	0	15
        "  gene2:A vs. gene4:B"	3	1	2	1.265	2	2	2.236	15
        "  gene2:A vs. gene4:C"	3	2.5	0.5	1.265	2	2	0.559	15
        "  gene2:A vs. gene5:A"	3	2.5	0.5	1.265	2	2	0.559	15
        "  gene2:A vs. gene5:B"	3	3.5	-0.5	1.265	2	2	0.559	15
        "  gene2:A vs. gene5:C"	3	4.5	-1.5	1.265	2	2	1.677	15
        "  gene2:B vs. gene2:C"	1.5	4	-2.5	1.265	2	2	2.795	15
        "  gene2:B vs. gene3:A"	1.5	2.5	-1	1.265	2	2	1.118	15
        "  gene2:B vs. gene3:B"	1.5	2	-0.5	1.265	2	2	0.559	15
        "  gene2:B vs. gene3:C"	1.5	2.5	-1	1.265	2	2	1.118	15
        "  gene2:B vs. gene4:A"	1.5	3	-1.5	1.265	2	2	1.677	15
        "  gene2:B vs. gene4:B"	1.5	1	0.5	1.265	2	2	0.559	15
        "  gene2:B vs. gene4:C"	1.5	2.5	-1	1.265	2	2	1.118	15
        "  gene2:B vs. gene5:A"	1.5	2.5	-1	1.265	2	2	1.118	15
        "  gene2:B vs. gene5:B"	1.5	3.5	-2	1.265	2	2	2.236	15
        "  gene2:B vs. gene5:C"	1.5	4.5	-3	1.265	2	2	3.354	15
        "  gene2:C vs. gene3:A"	4	2.5	1.5	1.265	2	2	1.677	15
        "  gene2:C vs. gene3:B"	4	2	2	1.265	2	2	2.236	15
        "  gene2:C vs. gene3:C"	4	2.5	1.5	1.265	2	2	1.677	15
        "  gene2:C vs. gene4:A"	4	3	1	1.265	2	2	1.118	15
        "  gene2:C vs. gene4:B"	4	1	3	1.265	2	2	3.354	15
        "  gene2:C vs. gene4:C"	4	2.5	1.5	1.265	2	2	1.677	15
        "  gene2:C vs. gene5:A"	4	2.5	1.5	1.265	2	2	1.677	15
        "  gene2:C vs. gene5:B"	4	3.5	0.5	1.265	2	2	0.559	15
        "  gene2:C vs. gene5:C"	4	4.5	-0.5	1.265	2	2	0.559	15
        "  gene3:A vs. gene3:B"	2.5	2	0.5	1.265	2	2	0.559	15
        "  gene3:A vs. gene3:C"	2.5	2.5	0	1.265	2	2	0	15
        "  gene3:A vs. gene4:A"	2.5	3	-0.5	1.265	2	2	0.559	15
        "  gene3:A vs. gene4:B"	2.5	1	1.5	1.265	2	2	1.677	15
        "  gene3:A vs. gene4:C"	2.5	2.5	0	1.265	2	2	0	15
        "  gene3:A vs. gene5:A"	2.5	2.5	0	1.265	2	2	0	15
        "  gene3:A vs. gene5:B"	2.5	3.5	-1	1.265	2	2	1.118	15
        "  gene3:A vs. gene5:C"	2.5	4.5	-2	1.265	2	2	2.236	15
        "  gene3:B vs. gene3:C"	2	2.5	-0.5	1.265	2	2	0.559	15
        "  gene3:B vs. gene4:A"	2	3	-1	1.265	2	2	1.118	15
        "  gene3:B vs. gene4:B"	2	1	1	1.265	2	2	1.118	15
        "  gene3:B vs. gene4:C"	2	2.5	-0.5	1.265	2	2	0.559	15
        "  gene3:B vs. gene5:A"	2	2.5	-0.5	1.265	2	2	0.559	15
        "  gene3:B vs. gene5:B"	2	3.5	-1.5	1.265	2	2	1.677	15
        "  gene3:B vs. gene5:C"	2	4.5	-2.5	1.265	2	2	2.795	15
        "  gene3:C vs. gene4:A"	2.5	3	-0.5	1.265	2	2	0.559	15
        "  gene3:C vs. gene4:B"	2.5	1	1.5	1.265	2	2	1.677	15
        "  gene3:C vs. gene4:C"	2.5	2.5	0	1.265	2	2	0	15
        "  gene3:C vs. gene5:A"	2.5	2.5	0	1.265	2	2	0	15
        "  gene3:C vs. gene5:B"	2.5	3.5	-1	1.265	2	2	1.118	15
        "  gene3:C vs. gene5:C"	2.5	4.5	-2	1.265	2	2	2.236	15
        "  gene4:A vs. gene4:B"	3	1	2	1.265	2	2	2.236	15
        "  gene4:A vs. gene4:C"	3	2.5	0.5	1.265	2	2	0.559	15
        "  gene4:A vs. gene5:A"	3	2.5	0.5	1.265	2	2	0.559	15
        "  gene4:A vs. gene5:B"	3	3.5	-0.5	1.265	2	2	0.559	15
        "  gene4:A vs. gene5:C"	3	4.5	-1.5	1.265	2	2	1.677	15
        "  gene4:B vs. gene4:C"	1	2.5	-1.5	1.265	2	2	1.677	15
        "  gene4:B vs. gene5:A"	1	2.5	-1.5	1.265	2	2	1.677	15
        "  gene4:B vs. gene5:B"	1	3.5	-2.5	1.265	2	2	2.795	15
        "  gene4:B vs. gene5:C"	1	4.5	-3.5	1.265	2	2	3.913	15
        "  gene4:C vs. gene5:A"	2.5	2.5	0	1.265	2	2	0	15
        "  gene4:C vs. gene5:B"	2.5	3.5	-1	1.265	2	2	1.118	15
        "  gene4:C vs. gene5:C"	2.5	4.5	-2	1.265	2	2	2.236	15
        "  gene5:A vs. gene5:B"	2.5	3.5	-1	1.265	2	2	1.118	15
        "  gene5:A vs. gene5:C"	2.5	4.5	-2	1.265	2	2	2.236	15
        "  gene5:B vs. gene5:C"	3.5	4.5	-1	1.265	2	2	1.118	15

* To see how do this in R, skip to the last section.

#### Multiple Hypothesis Testing in R using Tukey HSD
* Repeat the steps above under Two-Way ANOVA using R
* Then, run this command:

        TukeyHSD(anovaResults)

* You will see this very long output.  Annotations have been added that describe the equivalent analysis in GraphPad Prism, as well as notes to be mindful of, if applicable.

        > TukeyHSD(aDataM)
          Tukey multiple comparisons of means
            95% family-wise confidence level

        Fit: aov(formula = Values ~ Group * Genes, data = anovaDF)
        
        # NOTE: $Group is the same as "Compare column means (main column effect)" in GraphPad Prism.  
        # The values may be inverted if comparing B-A instead of A-B (etc).
        $Group
            diff         lwr      upr     p adj
        B-A -0.4 -1.86935105 1.069351 0.7630632
        C-A  1.0 -0.46935105 2.469351 0.2138022
        C-B  1.4 -0.06935105 2.869351 0.0629154
        
        # NOTE: $Genes is the same as "Compare row means (main row effect)" in GraphPad Prism.
        # The values may be inverted if comparing gene2-gene1 instead of gene1-gene2 (etc).
        $Genes
                          diff        lwr      upr     p adj
        gene2-gene1 -0.3333333 -2.5884345 1.921768 0.9901263
        gene3-gene1 -0.8333333 -3.0884345 1.421768 0.7828976
        gene4-gene1 -1.0000000 -3.2551012 1.255101 0.6549241
        gene5-gene1  0.3333333 -1.9217679 2.588435 0.9901263
        gene3-gene2 -0.5000000 -2.7551012 1.755101 0.9567935
        gene4-gene2 -0.6666667 -2.9217679 1.588435 0.8875621
        gene5-gene2  0.6666667 -1.5884345 2.921768 0.8875621
        gene4-gene3 -0.1666667 -2.4217679 2.088435 0.9993245
        gene5-gene3  1.1666667 -1.0884345 3.421768 0.5207681
        gene5-gene4  1.3333333 -0.9217679 3.588435 0.3955944
        
        # NOTE1: Group:Genes is the same as "Compare cell means regardless of rows and columns" in GraphPad Prism.
        # The values may be inverted if comparing B:gene2-B:gene1 instead of B:gene1-B:gene2 (etc).
        # Caveat to NOTE1: R will [only] exhaustively contrast all genes among all groups, as well as the above contrasts, but there is no simple way to perform other multiple hypothesis tests that GraphPad Prism performs.   

        $`Group:Genes`
                                 diff       lwr      upr     p adj
        B:gene1-A:gene1  1.000000e+00 -4.052884 6.052884 0.9998977
        C:gene1-A:gene1  2.500000e+00 -2.552884 7.552884 0.7847384
        A:gene2-A:gene1  1.000000e+00 -4.052884 6.052884 0.9998977
        B:gene2-A:gene1 -5.000000e-01 -5.552884 4.552884 1.0000000
        C:gene2-A:gene1  2.000000e+00 -3.052884 7.052884 0.9390681
        A:gene3-A:gene1  5.000000e-01 -4.552884 5.552884 1.0000000
        B:gene3-A:gene1  4.440892e-16 -5.052884 5.052884 1.0000000
        C:gene3-A:gene1  5.000000e-01 -4.552884 5.552884 1.0000000
        A:gene4-A:gene1  1.000000e+00 -4.052884 6.052884 0.9998977
        B:gene4-A:gene1 -1.000000e+00 -6.052884 4.052884 0.9998977
        C:gene4-A:gene1  5.000000e-01 -4.552884 5.552884 1.0000000
        A:gene5-A:gene1  5.000000e-01 -4.552884 5.552884 1.0000000
        B:gene5-A:gene1  1.500000e+00 -3.552884 6.552884 0.9935688
        C:gene5-A:gene1  2.500000e+00 -2.552884 7.552884 0.7847384
        C:gene1-B:gene1  1.500000e+00 -3.552884 6.552884 0.9935688
        A:gene2-B:gene1 -8.881784e-16 -5.052884 5.052884 1.0000000
        B:gene2-B:gene1 -1.500000e+00 -6.552884 3.552884 0.9935688
        C:gene2-B:gene1  1.000000e+00 -4.052884 6.052884 0.9998977
        A:gene3-B:gene1 -5.000000e-01 -5.552884 4.552884 1.0000000
        B:gene3-B:gene1 -1.000000e+00 -6.052884 4.052884 0.9998977
        C:gene3-B:gene1 -5.000000e-01 -5.552884 4.552884 1.0000000
        A:gene4-B:gene1  8.881784e-16 -5.052884 5.052884 1.0000000
        B:gene4-B:gene1 -2.000000e+00 -7.052884 3.052884 0.9390681
        C:gene4-B:gene1 -5.000000e-01 -5.552884 4.552884 1.0000000
        A:gene5-B:gene1 -5.000000e-01 -5.552884 4.552884 1.0000000
        B:gene5-B:gene1  5.000000e-01 -4.552884 5.552884 1.0000000
        C:gene5-B:gene1  1.500000e+00 -3.552884 6.552884 0.9935688
        A:gene2-C:gene1 -1.500000e+00 -6.552884 3.552884 0.9935688
        B:gene2-C:gene1 -3.000000e+00 -8.052884 2.052884 0.5624868
        C:gene2-C:gene1 -5.000000e-01 -5.552884 4.552884 1.0000000
        A:gene3-C:gene1 -2.000000e+00 -7.052884 3.052884 0.9390681
        B:gene3-C:gene1 -2.500000e+00 -7.552884 2.552884 0.7847384
        C:gene3-C:gene1 -2.000000e+00 -7.052884 3.052884 0.9390681
        A:gene4-C:gene1 -1.500000e+00 -6.552884 3.552884 0.9935688
        B:gene4-C:gene1 -3.500000e+00 -8.552884 1.552884 0.3522818
        C:gene4-C:gene1 -2.000000e+00 -7.052884 3.052884 0.9390681
        A:gene5-C:gene1 -2.000000e+00 -7.052884 3.052884 0.9390681
        B:gene5-C:gene1 -1.000000e+00 -6.052884 4.052884 0.9998977
        C:gene5-C:gene1  8.881784e-16 -5.052884 5.052884 1.0000000
        B:gene2-A:gene2 -1.500000e+00 -6.552884 3.552884 0.9935688
        C:gene2-A:gene2  1.000000e+00 -4.052884 6.052884 0.9998977
        A:gene3-A:gene2 -5.000000e-01 -5.552884 4.552884 1.0000000
        B:gene3-A:gene2 -1.000000e+00 -6.052884 4.052884 0.9998977
        C:gene3-A:gene2 -5.000000e-01 -5.552884 4.552884 1.0000000
        A:gene4-A:gene2  1.776357e-15 -5.052884 5.052884 1.0000000
        B:gene4-A:gene2 -2.000000e+00 -7.052884 3.052884 0.9390681
        C:gene4-A:gene2 -5.000000e-01 -5.552884 4.552884 1.0000000
        A:gene5-A:gene2 -5.000000e-01 -5.552884 4.552884 1.0000000
        B:gene5-A:gene2  5.000000e-01 -4.552884 5.552884 1.0000000
        C:gene5-A:gene2  1.500000e+00 -3.552884 6.552884 0.9935688
        C:gene2-B:gene2  2.500000e+00 -2.552884 7.552884 0.7847384
        A:gene3-B:gene2  1.000000e+00 -4.052884 6.052884 0.9998977
        B:gene3-B:gene2  5.000000e-01 -4.552884 5.552884 1.0000000
        C:gene3-B:gene2  1.000000e+00 -4.052884 6.052884 0.9998977
        A:gene4-B:gene2  1.500000e+00 -3.552884 6.552884 0.9935688
        B:gene4-B:gene2 -5.000000e-01 -5.552884 4.552884 1.0000000
        C:gene4-B:gene2  1.000000e+00 -4.052884 6.052884 0.9998977
        A:gene5-B:gene2  1.000000e+00 -4.052884 6.052884 0.9998977
        B:gene5-B:gene2  2.000000e+00 -3.052884 7.052884 0.9390681
        C:gene5-B:gene2  3.000000e+00 -2.052884 8.052884 0.5624868
        A:gene3-C:gene2 -1.500000e+00 -6.552884 3.552884 0.9935688
        B:gene3-C:gene2 -2.000000e+00 -7.052884 3.052884 0.9390681
        C:gene3-C:gene2 -1.500000e+00 -6.552884 3.552884 0.9935688
        A:gene4-C:gene2 -1.000000e+00 -6.052884 4.052884 0.9998977
        B:gene4-C:gene2 -3.000000e+00 -8.052884 2.052884 0.5624868
        C:gene4-C:gene2 -1.500000e+00 -6.552884 3.552884 0.9935688
        A:gene5-C:gene2 -1.500000e+00 -6.552884 3.552884 0.9935688
        B:gene5-C:gene2 -5.000000e-01 -5.552884 4.552884 1.0000000
        C:gene5-C:gene2  5.000000e-01 -4.552884 5.552884 1.0000000
        B:gene3-A:gene3 -5.000000e-01 -5.552884 4.552884 1.0000000
        C:gene3-A:gene3 -1.332268e-15 -5.052884 5.052884 1.0000000
        A:gene4-A:gene3  5.000000e-01 -4.552884 5.552884 1.0000000
        B:gene4-A:gene3 -1.500000e+00 -6.552884 3.552884 0.9935688
        C:gene4-A:gene3 -8.881784e-16 -5.052884 5.052884 1.0000000
        A:gene5-A:gene3 -8.881784e-16 -5.052884 5.052884 1.0000000
        B:gene5-A:gene3  1.000000e+00 -4.052884 6.052884 0.9998977
        C:gene5-A:gene3  2.000000e+00 -3.052884 7.052884 0.9390681
        C:gene3-B:gene3  5.000000e-01 -4.552884 5.552884 1.0000000
        A:gene4-B:gene3  1.000000e+00 -4.052884 6.052884 0.9998977
        B:gene4-B:gene3 -1.000000e+00 -6.052884 4.052884 0.9998977
        C:gene4-B:gene3  5.000000e-01 -4.552884 5.552884 1.0000000
        A:gene5-B:gene3  5.000000e-01 -4.552884 5.552884 1.0000000
        B:gene5-B:gene3  1.500000e+00 -3.552884 6.552884 0.9935688
        C:gene5-B:gene3  2.500000e+00 -2.552884 7.552884 0.7847384
        A:gene4-C:gene3  5.000000e-01 -4.552884 5.552884 1.0000000
        B:gene4-C:gene3 -1.500000e+00 -6.552884 3.552884 0.9935688
        C:gene4-C:gene3  4.440892e-16 -5.052884 5.052884 1.0000000
        A:gene5-C:gene3  4.440892e-16 -5.052884 5.052884 1.0000000
        B:gene5-C:gene3  1.000000e+00 -4.052884 6.052884 0.9998977
        C:gene5-C:gene3  2.000000e+00 -3.052884 7.052884 0.9390681
        B:gene4-A:gene4 -2.000000e+00 -7.052884 3.052884 0.9390681
        C:gene4-A:gene4 -5.000000e-01 -5.552884 4.552884 1.0000000
        A:gene5-A:gene4 -5.000000e-01 -5.552884 4.552884 1.0000000
        B:gene5-A:gene4  5.000000e-01 -4.552884 5.552884 1.0000000
        C:gene5-A:gene4  1.500000e+00 -3.552884 6.552884 0.9935688
        C:gene4-B:gene4  1.500000e+00 -3.552884 6.552884 0.9935688
        A:gene5-B:gene4  1.500000e+00 -3.552884 6.552884 0.9935688
        B:gene5-B:gene4  2.500000e+00 -2.552884 7.552884 0.7847384
        C:gene5-B:gene4  3.500000e+00 -1.552884 8.552884 0.3522818
        A:gene5-C:gene4  0.000000e+00 -5.052884 5.052884 1.0000000
        B:gene5-C:gene4  1.000000e+00 -4.052884 6.052884 0.9998977
        C:gene5-C:gene4  2.000000e+00 -3.052884 7.052884 0.9390681
        B:gene5-A:gene5  1.000000e+00 -4.052884 6.052884 0.9998977
        C:gene5-A:gene5  2.000000e+00 -3.052884 7.052884 0.9390681
        C:gene5-B:gene5  1.000000e+00 -4.052884 6.052884 0.9998977


Optional Challenge question:
1) Is the data *actually* normally distributed?  How can you check for this, and if it is not normally distributed, how should you modify the analysis?
