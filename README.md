# School_District_Analysis
## Overview
Our client, Maria, had asked for an analysis of school performance in her district to help inform future decisions on spending, class size, and school type.  The school board has subsequently found evidence of academic dishonesty in the math and reading scores of 9th graders at Thomas High School (THS). The purose of this analysis is to clean the original data of any potentially dishonest scores and re-submit the analysis, highlighting any changes in the output after cleaning the data.

### Deliverables
 - Replace Thomas High School (THS) reading and math scores for the 9th graders with NaN 
 - District Summary 
 - School Summary
 - Top 5 and bottom 5 performaing schools based on overall passing rate
 - Average math and reading scores by grade level from each school
 - Average scores and percent passing by budget, by school size, and by school type
 
## Results

### Impact to District Summary
The overall impact of the falsified 9th grade math and reading scores from Thomas High School (THS) to the district numbers was nominal.  The total number of students in the district is over 39k, and the 9th graders at THS are only a sliver of that at 461.  This is such a small percentage of the total sampple that we would not expect significant impact at the district level: there was no change to the average reading score and the other scores and percentage of passing students were only impacted by 0.1 to 0.3%.  See the summary and Data Frame image below.

#### District Metrics Before and After Cleaning:
  - Average math score: 79.0 vs 78.9, 0.1 lower after clenaing
  - Average reading score: 81.9 vs 81.9, no change after cleaning
  - Percent Passing Math: 75% vs 74.8%, 0.2% lower after cleaning
  - Percent Passing Reaiding: 86% vs 85.7%, 0.3% lower after cleaning
  - Percent Overall Passiing: 65% vs 64.9%, 0.1% lower after cleaning

#### District Summary DataFrame Before and After Cleaning
<img width="362" alt="District Summary" src="https://user-images.githubusercontent.com/93740725/150449635-dc5e773b-df1b-4f01-a091-d754fae15846.png">

### Impact to School Summary
The impact of the falsified data at THS was also nominal once we removed the 9th graders' scores and adjusted the total number of students accordingly.  The falsified data likely swapped in average scores for any failing scores, which explains why the averages didn't change but the passing rates at THS dropped after the data was cleaned. 

#### Per School Metrics Before and After Cleaning:
  - Average math score: 83.42 vs 83.35, 0.07 lower after clenaing
  - Average reading score: 83.85 vs 83.90, 0.05 lower after cleaning
  - Percent Passing Math: 93.27% vs 93.19%, 0.08% lower after cleaning
  - Percent Passing Reaiding: 97.31% vs 97.02%, 0.29% lower after cleaning
  - Percent Overall Passiing: 90.95% vs 90.63%, 0.32% lower after cleaning

#### School Summary DataFrame Before and After Cleaning
<img width="462" alt="School Summary" src="https://user-images.githubusercontent.com/93740725/150455328-22c758e3-2ec8-4f1c-b4a2-dae0423d3319.png">

### Impact to THS's Performance Relative to Other Schools
Thomas High School ranked number 2 in the top five schools both before and after the falsified data was cleaned, but with a smaller margin over the number 3 school after cleaning. 

<img width="877" alt="Top 5" src="https://user-images.githubusercontent.com/93740725/150458324-730decf6-0781-4a3d-815d-8759728e2ecf.png">

### Impact to scores by grade, scores by spending, scores by size, and scores by type
Replacing THS 9th grade math and reading scores with NaN did not impact the scores by spending, scores by school size, or scores by school type DataFrames.  The only impact it had on the DataFrames containing math and reading scores by grade is that the 9th grade average for THS showed NaN instead of numeric values. 

<img width="346" alt="Math Scores by Grade" src="https://user-images.githubusercontent.com/93740725/150459930-1f07c2c4-ccd1-48e6-b771-e02a7073b4c7.png">

### Explanation of Code
#### Replace Reading and Math Scores
     #Import numpy 
     import numpy as np
     
     #Use the loc method on the student_data_df to select all the reading 
     and math scores from the 9th grade at THS and replace them with NaN.

     student_data_df.loc[
         (student_data_df["school_name"] =="Thomas High School") 
         & (student_data_df["grade"] == "9th")
         & (student_data_df["reading_score"] >0),
                     "reading_score"] = np.NaN
     student_data_df.loc[
         (student_data_df["school_name"] =="Thomas High School") 
         & (student_data_df["grade"] == "9th")
         & (student_data_df["math_score"] >0),
                 "math_score"] = np.NaN

<img width="344" alt="Check for NaNs" src="https://user-images.githubusercontent.com/93740725/150592945-062ac02a-ccf6-46ae-ba8e-f343f389dda7.png">

#### Recalculate District Summary
     #Combine the data into a single dataset
     school_data_complete_df = pd.merge(student_data_df, school_data_df, how="left", on=["school_name", "school_name"])
     
     #Calculate the Totals (Schools and Students)
     school_count = len(school_data_complete_df["school_name"].unique())
     student_count = school_data_complete_df["Student ID"].count()

     #Calculate the Total Budget
     total_budget = school_data_df["budget"].sum()

     #Calculate the Average Scores using the "clean_student_data".
     average_reading_score = school_data_complete_df["reading_score"].mean()
     average_math_score = school_data_complete_df["math_score"].mean()

     #Update the total student count to remove NaN students
     
     #Step 1. Get the number of students that are in ninth grade at Thomas High School
     THS_9th_count = school_data_complete_df["math_score"].isnull().sum()
     #Get the total student count
     student_count = school_data_complete_df["Student ID"].count()
     #Step 2. Subtract the number of students that are in ninth grade at Thomas High School 
     from the total student count to get the new total student count.
     updated_total_student_count = student_count - THS_9th_count

     #Calculate the passing rates using the "clean_student_data".
     passing_math_count = school_data_complete_df[(school_data_complete_df["math_score"] >= 70)].count()["student_name"]
     passing_reading_count = school_data_complete_df[(school_data_complete_df["reading_score"] >= 70)].count()["student_name"]
     
     #Step 3. Calculate the passing percentages with the new total student count.
     passing_math_percentage = passing_math_count / updated_total_student_count *100
     passing_reading_percentage = passing_reading_count / updated_total_student_count *100
     #Calculate the students who passed both reading and math.
     passing_math_reading = school_data_complete_df[(school_data_complete_df["math_score"] >= 70)
                                                    & (school_data_complete_df["reading_score"] >= 70)]
     
     #Calculate the number of students that passed both reading and math.
     overall_passing_math_reading_count = passing_math_reading["student_name"].count()
     
     #Step 4.Calculate the overall passing percentage with new total student count.
     overall_passing_percentage = overall_passing_math_reading_count / updated_total_student_count *100

     #Create a DataFrame
     district_summary_df = pd.DataFrame(
               [{"Total Schools": school_count, 
               "Total Students": student_count, 
               "Total Budget": total_budget,
               "Average Math Score": average_math_score, 
               "Average Reading Score": average_reading_score,
               "% Passing Math": passing_math_percentage,
              "% Passing Reading": passing_reading_percentage,
             "% Overall Passing": overall_passing_percentage}])

     #Format 
     district_summary_df["Total Students"] = district_summary_df["Total Students"].map("{:,}".format)
     district_summary_df["Total Budget"] = district_summary_df["Total Budget"].map("${:,.2f}".format)
     district_summary_df["Average Math Score"] = district_summary_df["Average Math Score"].map("{:.1f}".format)
     district_summary_df["Average Reading Score"] = district_summary_df["Average Reading Score"].map("{:.1f}".format)
     district_summary_df["% Passing Math"] = district_summary_df["% Passing Math"].map("{:.1f}".format)
     district_summary_df["% Passing Reading"] = district_summary_df["% Passing Reading"].map("{:.1f}".format)
     district_summary_df["% Overall Passing"] = district_summary_df["% Overall Passing"].map("{:.1f}".format)

<img width="423" alt="District Summary CLEAN" src="https://user-images.githubusercontent.com/93740725/150593417-68155acc-b8a3-4dea-a363-5b5ec0a3d49a.png">

#### Recalculate Per-School Summary
     #Determine the School Type
     per_school_types = school_data_df.set_index(["school_name"])["type"]

     #Calculate the total student count.
     per_school_counts = school_data_complete_df["school_name"].value_counts()

     #Calculate the total school budget and per capita spending
     per_school_budget = school_data_complete_df.groupby(["school_name"]).mean()["budget"]
     per_school_capita = per_school_budget / per_school_counts

     #Calculate the average test scores.
     per_school_math = school_data_complete_df.groupby(["school_name"]).mean()["math_score"]
     per_school_reading = school_data_complete_df.groupby(["school_name"]).mean()["reading_score"]

     #Calculate the passing scores by creating a filtered DataFrame.
     per_school_passing_math = school_data_complete_df[(school_data_complete_df["math_score"] >= 70)]
     per_school_passing_reading = school_data_complete_df[(school_data_complete_df["reading_score"] >= 70)]

     #Calculate the number of students passing math and passing reading by school.
     per_school_passing_math = per_school_passing_math.groupby(["school_name"]).count()["student_name"]
     per_school_passing_reading = per_school_passing_reading.groupby(["school_name"]).count()["student_name"]

     #Calculate the percentage of passing math and reading scores per school.
     per_school_passing_math = per_school_passing_math / per_school_counts * 100
     per_school_passing_reading = per_school_passing_reading / per_school_counts * 100

     #Calculate the students who passed both reading and math.
     per_passing_math_reading = school_data_complete_df[(school_data_complete_df["reading_score"] >= 70)
                                                    & (school_data_complete_df["math_score"] >= 70)]
     #Calculate the number of students passing math and passing reading by school.
     per_passing_math_reading = per_passing_math_reading.groupby(["school_name"]).count()["student_name"]

     #Calculate the percentage of passing math and reading scores per school.
     per_overall_passing_percentage = per_passing_math_reading / per_school_counts * 100

     #Create the DataFrame
     per_school_summary_df = pd.DataFrame({
         "School Type": per_school_types,
         "Total Students": per_school_counts,
         "Total School Budget": per_school_budget,
         "Per Student Budget": per_school_capita,
         "Average Math Score": per_school_math,
         "Average Reading Score": per_school_reading,
         "% Passing Math": per_school_passing_math,
         "% Passing Reading": per_school_passing_reading,
         "% Overall Passing": per_overall_passing_percentage})
     
     #Format
     per_school_summary_df["Per Student Budget"] = per_school_summary_df["Per Student Budget"].map("${:,.2f}".format)
     per_school_summary_df["Total School Budget"] = per_school_summary_df["Total School Budget"].map("${:,.2f}".format)

#### Update the student totals for THS and swap them into the School Summary DataFrame
     
     #Step 5.  Get the number of 10th-12th graders from Thomas High School (THS).
     THS_10to12 = student_data_df.loc[
         (student_data_df["school_name"] =="Thomas High School") 
         & (student_data_df["grade"] != "9th")] 
     THS_10to12_count = THS_10to12["Student ID"].count()

     #Step 6. Get all the students passing math from THS
     THS_passing_math = student_data_df.loc[
         (student_data_df["school_name"] =="Thomas High School") 
         & (student_data_df["math_score"] >=70)]
     THS_passing_math["student_name"].count()

     #Step 7. Get all the students passing reading from THS
     THS_passing_reading = student_data_df.loc[
         (student_data_df["school_name"] =="Thomas High School") 
         & (student_data_df["reading_score"]>=70)]
     THS_passing_reading["student_name"].count()
 
     #Step 8. Get all the students passing math and reading from THS
     THS_passing_math_reading = student_data_df.loc[
         (student_data_df["school_name"] =="Thomas High School") 
         & (student_data_df["math_score"] >=70)
         & (student_data_df["reading_score"]>=70)]
     THS_pass_math_read_count = THS_passing_math_reading["student_name"].count()
     
     #Step 9. Calculate the percentage of 10th-12th grade students passing math from Thomas High School. 
     percent_THS_10to12_pass_math = THS_passing_math["student_name"].count() / THS_10to12_count *100
     
     #Step 10. Calculate the percentage of 10th-12th grade students passing reading from Thomas High School.
     percent_THS_10to12_pass_read = THS_passing_reading["student_name"].count() / THS_10to12_count *100
     
     #Step 11. Calculate the overall passing percentage of 10th-12th grade from Thomas High School. 
     percent_pass_overall_THS = THS_pass_math_read_count / THS_10to12_count *100
         
     # Step 12. Replace the passing math percent for Thomas High School in the per_school_summary_df.
     per_school_summary_df.loc["Thomas High School", "% Passing Math"] =  percent_THS_10to12_pass_math 
     
     #Step 13. Replace the passing reading percentage for Thomas High School in the per_school_summary_df.
     per_school_summary_df.loc["Thomas High School", "% Passing Reading"] =  percent_THS_10to12_pass_read 
     
     # Step 14. Replace the overall passing percentage for Thomas High School in the per_school_summary_df.
     per_school_summary_df.loc["Thomas High School", "% Overall Passing"] =  percent_pass_overall_THS
     
     per_school_summary.df
   
<img width="424" alt="School Summary CLEAN" src="https://user-images.githubusercontent.com/93740725/150594015-1386b34a-7fc4-40e5-a859-ad77a67cdf0e.png">

#### Recalculate High and Low Performaing Schools
     #Sort and show top five schools.
     top_schools = per_school_summary_df.sort_values(["% Overall Passing"], ascending=False)
     top_schools.head()

     #Sort and show bottom 5 schools
     bottom_schools = per_school_summary_df.sort_values(["% Overall Passing"], ascending=True)
     bottom_schools.head()

<img width="424" alt="Top and Bottom CLEAN" src="https://user-images.githubusercontent.com/93740725/150594725-ab970d9f-ac1d-4e59-a464-774b28b35a39.png">
  
 #### Recalculate Math and Reading Scores by Grade
      #Create a Series of scores by grade levels using conditionals.
     scores_by_9th = school_data_complete_df.loc[school_data_complete_df["grade"] =="9th"]
     scores_by_10th = school_data_complete_df.loc[school_data_complete_df["grade"] =="10th"]
     scores_by_11th = school_data_complete_df.loc[school_data_complete_df["grade"] =="11th"]
     scores_by_12th = school_data_complete_df.loc[school_data_complete_df["grade"] =="12th"]

     #Group each school Series by the school name for the average math score.
     math_by_9th = scores_by_9th.groupby(["school_name"]).mean()["math_score"]
     math_by_10th = scores_by_10th.groupby(["school_name"]).mean()["math_score"]
     math_by_11th = scores_by_11th.groupby(["school_name"]).mean()["math_score"]
     math_by_12th = scores_by_12th.groupby(["school_name"]).mean()["math_score"]

     #Group each school Series by the school name for the average reading score.
     reading_by_9th = scores_by_9th.groupby(["school_name"]).mean()["reading_score"]
     reading_by_10th = scores_by_10th.groupby(["school_name"]).mean()["reading_score"]
     reading_by_11th = scores_by_11th.groupby(["school_name"]).mean()["reading_score"]
     reading_by_12th = scores_by_12th.groupby(["school_name"]).mean()["reading_score"]

     #Combine each Series for average math scores by school into single data frame.
     math_by_grade = pd.DataFrame({
                    "9th": math_by_9th,
                    "10th": math_by_10th,
                    "11th": math_by_11th,
                    "12th": math_by_12th})

     #Combine each Series for average reading scores by school into single data frame.
     reading_by_grade = pd.DataFrame({
                    "9th": reading_by_9th,
                    "10th": reading_by_10th,
                    "11th": reading_by_11th,
                    "12th": reading_by_12th})

     #Format each grade column.
     math_by_grade["9th"] = math_by_grade["9th"].map("{:.1f}".format)
     math_by_grade["10th"] = math_by_grade["10th"].map("{:.1f}".format)
     math_by_grade["11th"] = math_by_grade["11th"].map("{:.1f}".format)
     math_by_grade["12th"] = math_by_grade["12th"].map("{:.1f}".format)

     reading_by_grade["9th"] = reading_by_grade["9th"].map("{:.1f}".format)
     reading_by_grade["10th"] = reading_by_grade["10th"].map("{:.1f}".format)
     reading_by_grade["11th"] = reading_by_grade["11th"].map("{:.1f}".format)
     reading_by_grade["12th"] = reading_by_grade["12th"].map("{:.1f}".format)

     #Remove the index name.
     math_by_grade.index.name = None
     #Display the DataFrame.
     math_by_grade

     #Remove the index name.
     reading_by_grade.index.name = None
     #Display the DataFrame.
     reading_by_grade

<img width="336" alt="Scores by Grade CLEAN" src="https://user-images.githubusercontent.com/93740725/150595366-48e96bd3-228c-49b5-82b9-de6e8579cde3.png">

#### Recaculate Scores by School Spending
     #Establish the spending bins and group names
     spending_bins = [0, 585, 630, 645, 675]
     group_names = ["<$584", "$585-629", "$630-644", "$645-675"]

     #Categorize spending based on the bins.
     per_school_summary_df["Spending Ranges (Per Student)"] = pd.cut(per_school_capita, spending_bins, labels=group_names)

     #Calculate averages by spending for the desired columns. 
     spending_math_scores = per_school_summary_df.groupby(["Spending Ranges (Per Student)"]).mean()["Average Math Score"]
     spending_reading_scores = per_school_summary_df.groupby(["Spending Ranges (Per Student)"]).mean()["Average Reading Score"]
     spending_passing_math = per_school_summary_df.groupby(["Spending Ranges (Per Student)"]).mean()["% Passing Math"]
     spending_passing_reading = per_school_summary_df.groupby(["Spending Ranges (Per Student)"]).mean()["% Passing Reading"]
     overall_passing_spending = per_school_summary_df.groupby(["Spending Ranges (Per Student)"]).mean()["% Overall Passing"]

     #Create the summary by spending DataFrame
     spending_summary_df = pd.DataFrame({
               "Average Math Score" : spending_math_scores,
               "Average Reading Score": spending_reading_scores,
               "% Passing Math": spending_passing_math,
               "% Passing Reading": spending_passing_reading,
               "% Overall Passing": overall_passing_spending})
     
     #Format the spending DataFrame 
     spending_summary_df["Average Math Score"] = spending_summary_df["Average Math Score"].map("{:.1f}".format)
     spending_summary_df["Average Reading Score"] = spending_summary_df["Average Reading Score"].map("{:.1f}".format)
     spending_summary_df["% Passing Math"] = spending_summary_df["% Passing Math"].map("{:.0f}".format)
     spending_summary_df["% Passing Reading"] = spending_summary_df["% Passing Reading"].map("{:.0f}".format)
     spending_summary_df["% Overall Passing"] = spending_summary_df["% Overall Passing"].map("{:.0f}".format)
     
     spending_summary_df

<img width="397" alt="Scores by Spending CLEAN" src="https://user-images.githubusercontent.com/93740725/150595614-023f1a69-78b9-4711-b841-6e26a024a19c.png">

#### Recalculate Scores by School Size
     #Establish the bins by school size.
     size_bins = [0, 1000, 2000, 5000]

     #Name the bins
     size_names = ["Small (<1000)", "Medium (1000-2000)", "Large (2000-5000)"]

     #Categorize spending based on the school size bins.
     per_school_summary_df["School Size"] = pd.cut(per_school_summary_df["Total Students"], size_bins, labels=size_names)

     #Calculate averages by school size for the desired columns.
     size_math_scores = per_school_summary_df.groupby(["School Size"]).mean()["Average Math Score"]
     size_reading_scores = per_school_summary_df.groupby(["School Size"]).mean()["Average Reading Score"]
     size_passing_math = per_school_summary_df.groupby(["School Size"]).mean()["% Passing Math"]
     size_passing_reading = per_school_summary_df.groupby(["School Size"]).mean()["% Passing Reading"]
     size_overall_passing = per_school_summary_df.groupby(["School Size"]).mean()["% Overall Passing"]

     #Assemble into summary by size DataFrame. 
     size_summary_df = pd.DataFrame({
               "Average Math Score" : size_math_scores,
               "Average Reading Score": size_reading_scores,
               "% Passing Math": size_passing_math,
               "% Passing Reading": size_passing_reading,
               "% Overall Passing": size_overall_passing})

     #Format the summary by size DataFrame  
     size_summary_df["Average Math Score"] = size_summary_df["Average Math Score"].map("{:.1f}".format)
     size_summary_df["Average Reading Score"] = size_summary_df["Average Reading Score"].map("{:.1f}".format)
     size_summary_df["% Passing Math"] = size_summary_df["% Passing Math"].map("{:.0f}".format)
     size_summary_df["% Passing Reading"] = size_summary_df["% Passing Reading"].map("{:.0f}".format)
     size_summary_df["% Overall Passing"] = size_summary_df["% Overall Passing"].map("{:.0f}".format)

     size_summary_df

<img width="412" alt="Scores by Size CLEAN" src="https://user-images.githubusercontent.com/93740725/150595759-d17b44c9-30f7-4c96-83a4-e07089332b4c.png">

#### Recalculate Scores by School Type
     #Calculate averages by school type for the desired columns.
     type_math_scores = per_school_summary_df.groupby(["School Type"]).mean()["Average Math Score"]
     type_reading_scores = per_school_summary_df.groupby(["School Type"]).mean()["Average Reading Score"]
     type_passing_math = per_school_summary_df.groupby(["School Type"]).mean()["% Passing Math"]
     type_passing_reading = per_school_summary_df.groupby(["School Type"]).mean()["% Passing Reading"]
     type_overall_passing = per_school_summary_df.groupby(["School Type"]).mean()["% Overall Passing"]

     #Asseble averages by school type into dataframe
     type_summary_df = pd.DataFrame({
               "Average Math Score" : type_math_scores,
               "Average Reading Score": type_reading_scores,
               "% Passing Math": type_passing_math,
               "% Passing Reading": type_passing_reading,
               "% Overall Passing": type_overall_passing})
     
     #formatting
     type_summary_df["Average Math Score"] = type_summary_df["Average Math Score"].map("{:.1f}".format)
     type_summary_df["Average Reading Score"] = type_summary_df["Average Reading Score"].map("{:.1f}".format)
     type_summary_df["% Passing Math"] = type_summary_df["% Passing Math"].map("{:.0f}".format)
     type_summary_df["% Passing Reading"] = type_summary_df["% Passing Reading"].map("{:.0f}".format)
     type_summary_df["% Overall Passing"] = type_summary_df["% Overall Passing"].map("{:.0f}".format)
     
     type_summary_df

<img width="389" alt="Scores by Type CLEAN" src="https://user-images.githubusercontent.com/93740725/150595869-813ad599-489f-45dd-9a5a-c31dc2895f32.png">

## Summary
Cleaning the falsified data from the 9th grade at Thomas High School resulted in a slight decrease to four of its performance metrics at the district and per-school levels:
  1. Decrease in average math scores
     - 0.1 at the district level
     - 0.07 at the per-school level
  2. Nominal impact to average reading scores
     - No change at he district level
     - 0.05 at the per-school level
  3. Decrease in the percent of students passing math
     - 0.2% at the district level
     - 0.08% at the per-school level
  4. Decrease in the percent of students passing reading
     - 0.3% at the district level
     - 0.28% at the per-school level
  5. Decrease in the overall percent of students passing
     - 0.1% at the district level
     - 0.32% at the per-school level
