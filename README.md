# School_District_Analysis
## Overview
Our client, Maria, had asked for an analysis of school performance in her district to help inform future decisions on spending, class size, and school type.  The school board has found evidence of academic dishonesty in the math and reading scores of 9th graders at Thomas High School (THS). The purose of this analysis is to clean the original data of any potentially dishonest scores and re-submit the analysis, highlighting any changes in the output after cleaning the data.

### Deliverables
 1. Replace Thomas High School (THS) reading and math scores for the 9th graders with NaN. 
 2. Create a District Summary that shows: 
     - The total number of schools and students
     - The total budget
     - Average math and reading scores
     - The percent of students passing math and reading
     - The percent of students passing both math and reading 
 4. Create a School Summary that shows the following per school:
     - The type of school (distrct or charter)
     - The student population
     - The school budget and per student budget
     - Average math and reading scores
     - Percent of students passing math and reading
     - Percent of students passing both math and reading 
 5. Top 5 performaing schools based on overall passing rate
 6. Bottom 5 performing schools based on overall passsing rate
 7. Average math scores by grade level from each school
 8. Average reading scores by grade level from each school
 9. Average scores and percent passing by budget
 10. Average scores and percent passing by school size
 11. Average scores and percent passing by school type
 
## Results

The overall impact of the falsified 9th grade math and reading scores from Thomas High School (THS) to the district numbers was nominal.  The total number of students in the district is over 39k, and the 9th graders at THS are only a sliver of that at 461.  This is such a small percentage of the total sampple that we would not expect significant impact at the district level.

### Impact to District Summary
The district-wide average math score was just 0.1% lower with the clean data, there was no impact to the reading score.  The percentage of students passing math was 0.2% lower with the clean data. The percentage of student passing reading decreased by 0.3% after cleaning the falsified data. The overall passing rate only dropped by 0.1% from 65 % with the falsified data to 64.9% with the clean data.  See the table below:

<img width="362" alt="District Summary" src="https://user-images.githubusercontent.com/93740725/150449635-dc5e773b-df1b-4f01-a091-d754fae15846.png">

### Impact to School Summary
The impact of the falsified data at THS was also nominal once we removed the 9th graders' scores and adjusted the total number of students accordingly.  The average math and reading scores of the cleaned data were within 0.0% and 0.1% of the falsified scores.  The percent of students who passed math dropped from 93.3% under the false data to an undramatic 93.2% with the clean data.  The percent of students who pssed reading dropped by 0.3% after the data was cleaned.  The percent of students who passed overall dropped 0.3% from 90.9% to 90.6% after the data was scrubbed.  The falsified data likely swapped in average scores for any failing scores, which explains why the averages didn't change but the passing rates at THS dropped after the data was cleaned. 

<img width="462" alt="School Summary" src="https://user-images.githubusercontent.com/93740725/150455328-22c758e3-2ec8-4f1c-b4a2-dae0423d3319.png">

### Impact to THS's Performance Relative to Other Schools
Thomas High School ranked number 2 in the top five schools both before and after the falsified data was cleaned.

<img width="877" alt="Top 5" src="https://user-images.githubusercontent.com/93740725/150458324-730decf6-0781-4a3d-815d-8759728e2ecf.png">

### Impact to scores by grade, scores by spending, scores by size, and scores by type
Replacing THS 9th grade math and reading scores with NaN did not impact the scores by spending, scores by school size, or scores by school type (charter vd district).  The only impact it had on the DataFrames containing math and reading scores by grade is that the 9th grade average for THS showed NaN instead of numeric value. 

<img width="346" alt="Math Scores by Grade" src="https://user-images.githubusercontent.com/93740725/150459930-1f07c2c4-ccd1-48e6-b771-e02a7073b4c7.png">

##Summary
