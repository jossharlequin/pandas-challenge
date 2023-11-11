# pandas-challenge
ReadMe
Throughout the whole code I code to switch formats of columns to do formulas. I wish I did it earlier and all at once, but I made an assumption about the data and I was wrong.

This code came from the class exercises and the code provided by the module.
# Dependencies and Setup
import pandas as pd
from pathlib import Path

# File to Load (Remember to Change These)
school_data_to_load = Path("Resources/schools_complete.csv")
student_data_to_load = Path("Resources/students_complete.csv")

# Read School and Student Data File and store into Pandas DataFrames
school_data = pd.read_csv(school_data_to_load)
student_data = pd.read_csv(student_data_to_load)

# Combine the data into a single dataset.  
school_data_complete = pd.merge(student_data, school_data, how="left", on=["school_name", "school_name"])
school_data_complete.head()

I got this code from the class exercises. I did this so I could copy/paste the columns name for the succeeding formulas.
#List column names so I can copy them easier
column_list = school_data_complete.columns
print(column_list)

I got this code here: https://www.tutorialspoint.com/python-pandas-get-unique-values-from-a-column#:~:text=To%20get%20unique%20values%20from,DataFrame%2C%20use%20the%20nunique().
# Calculate the total number of unique schools
school_count = school_data_complete.school_name.nunique()
print(school_count)

Got this from class exericses
# Calculate the total number of students
student_count = school_data_complete.student_name.count()
print(student_count)

From class exercises. I added the '$', but later it messed with the formulas. I just kept it in though.
# Calculate the total budget
total_budget = school_data_complete.budget.sum()
print('$',total_budget)

Class exercises
# Calculate the average (mean) math score
average_math_score = school_data_complete.math_score.mean()
print(average_math_score)

# Calculate the average (mean) reading score
average_reading_score = school_data_complete.reading_score.mean()
print(average_reading_score)

Code provided by module
# Use the following to calculate the percentage of students who passed math (math scores greather than or equal to 70)
passing_math_count = school_data_complete[(school_data_complete["math_score"] >= 70)].count()["student_name"]
passing_math_percentage = passing_math_count / float(student_count) * 100
print(passing_math_percentage)

# Calculate the percentage of students who passed reading (hint: look at how the math percentage was calculated)  
passing_reading_count = school_data_complete[(school_data_complete["reading_score"] >= 70)].count()["student_name"]
passing_reading_percentage = passing_reading_count / float(student_count) * 100
print(passing_reading_percentage)

# Use the following to calculate the percentage of students that passed math and reading
passing_math_reading_count = school_data_complete[
    (school_data_complete["math_score"] >= 70) & (school_data_complete["reading_score"] >= 70)
].count()["student_name"]
overall_passing_rate = passing_math_reading_count /  float(student_count) * 100
overall_passing_rate

pd.DataFrame is from class exercises. This spot I just put in the new variables/lists made earlier. I wish it told what it wanted me to list, but I asked other people, and these are the columns they had. I had to reformat some columns as well. Got help here: https://stackoverflow.com/questions/15891038/change-column-type-in-pandas
# Create a high-level snapshot of the district's key metrics in a DataFrame
district_summary = pd.DataFrame({
    "Total Schools": [school_count],
    "Total Students": [f"{student_count:,}"],
    "Total Budget": [f"${total_budget:,.2f}"],
    "Average Math Score": [average_math_score],
    "Average Reading Score": [average_reading_score],
    "% Passing Math": [passing_math_percentage],
    "% Passing Reading": [passing_reading_percentage],
    "% Overall Passing": [overall_passing_rate]
})

# Remove dollar signs and commas, then convert "Total Budget" to numeric
district_summary["Total Budget"] = pd.to_numeric(district_summary["Total Budget"].replace('[\$,]', '', regex=True))

# Convert "Total Students" to numeric
district_summary["Total Students"] = pd.to_numeric(district_summary["Total Students"].str.replace(',', ''))

# Formatting
district_summary["Total Students"] = district_summary["Total Students"].map("{:,.0f}".format)
district_summary["Total Budget"] = district_summary["Total Budget"].map("${:,}".format)

# Display the DataFrame
district_summary

The later formulas kept not liking the column formats. So I did a list to see them.
# Check the data types of the columns (I did this to troubleshoot errors I got making the district_summary dataframe)
print(district_summary.dtypes)

I did groupby because I tried a formula and it didn't work how I wanted. https://www.geeksforgeeks.org/python-pandas-dataframe-groupby/
# Calculate the total student count per school
per_school_counts = school_data_complete.groupby('school_name')['Student ID'].count()

# Display the school counts
print(per_school_counts)

# Calculate the total school budget and per capita spending per school
per_school_budget = school_data_complete.groupby('school_name')['budget'].count()
per_school_capita = per_school_budget / per_school_counts

# Display the results
print(per_school_budget)
print(per_school_capita)

# Calculate the average test scores per school
per_school_math = school_data_complete.groupby('school_name')['math_score'].mean()
per_school_reading = school_data_complete.groupby('school_name')['reading_score'].mean()
# Display the results
print(per_school_math)
print(per_school_reading)

I saw the formula further down on how to do the >=70 part and I used it here.
# Calculate the number of students per school with math scores of 70 or higher
students_passing_math = school_data_complete[school_data_complete['math_score'] >= 70].groupby('school_name')['Student ID'].count()
# I assume it wants a percentage
school_students_passing_math = (students_passing_math / per_school_counts) * 100
# Display the results
print(students_passing_math)
print(school_students_passing_math)

# Calculate the number of students per school with reading scores of 70 or higher
students_passing_reading = school_data_complete[school_data_complete['reading_score'] >= 70].groupby('school_name')['Student ID'].count()
# I assume it wants a percentage
school_students_passing_reading = (students_passing_reading / per_school_counts) * 100
# Display the results
print(students_passing_reading)
print(school_students_passing_reading)

Code provided
# Use the provided code to calculate the number of students per school that passed both math and reading with scores of 70 or higher
students_passing_math_and_reading = school_data_complete[
    (school_data_complete["reading_score"] >= 70) & (school_data_complete["math_score"] >= 70)
]
school_students_passing_math_and_reading = students_passing_math_and_reading.groupby(["school_name"]).size()
# Display the results
print(students_passing_math_and_reading)
print(school_students_passing_math_and_reading)

# Use the provided code to calculate the passing rates
per_school_passing_math = school_students_passing_math / per_school_counts * 100
per_school_passing_reading = school_students_passing_reading / per_school_counts * 100
overall_passing_rate = school_students_passing_math_and_reading / per_school_counts * 100
# Display the results
print(per_school_passing_math)
print(per_school_passing_reading)
print(overall_passing_rate)

Made another dataframe with the new columns.
# Create a DataFrame called `per_school_summary` with columns for the calculations above.
per_school_summary = pd.DataFrame({
    "Total Students": per_school_counts,
    "Total School Budget": per_school_budget,
    "Per Student Budget": per_school_capita,
    "Average Math Score": per_school_math,
    "Average Reading Score": per_school_reading,
    "% Passing Math": per_school_passing_math,
    "% Passing Reading": per_school_passing_reading,
    "% Overall Passing": overall_passing_rate
})

# Formatting
per_school_summary["Total School Budget"] = per_school_summary["Total School Budget"].map("${:,.2f}".format)
per_school_summary["Per Student Budget"] = per_school_summary["Per Student Budget"].map("${:,.2f}".format)

# Display the DataFrame
print(per_school_summary)

From class exercises
# Sort the schools by `% Overall Passing` in descending order and display the top 5 rows.
top_schools = per_school_summary.sort_values("% Overall Passing", ascending=False)
top_schools.head(5)

# Sort the schools by `% Overall Passing` in ascending order and display the top 5 rows.
bottom_schools = per_school_summary.sort_values("% Overall Passing", ascending=True)
bottom_schools.head(5)

Provided by module
# Use the code provided to separate the data by grade
ninth_graders = school_data_complete[(school_data_complete["grade"] == "9th")]
tenth_graders = school_data_complete[(school_data_complete["grade"] == "10th")]
eleventh_graders = school_data_complete[(school_data_complete["grade"] == "11th")]
twelfth_graders = school_data_complete[(school_data_complete["grade"] == "12th")]

# Group by `school_name` and take the mean of the `math_score` column for each grade.
ninth_grade_math_scores = ninth_graders.groupby('school_name')['math_score'].mean()
tenth_grader_math_scores = tenth_graders.groupby('school_name')['math_score'].mean()
eleventh_grader_math_scores = eleventh_graders.groupby('school_name')['math_score'].mean()
twelfth_grader_math_scores = twelfth_graders.groupby('school_name')['math_score'].mean()

# Combine each of the scores above into a single DataFrame called `math_scores_by_grade`
math_scores_by_grade = pd.DataFrame({
    "9th": ninth_grade_math_scores,
    "10th": tenth_grader_math_scores,
    "11th": eleventh_grader_math_scores,
    "12th": twelfth_grader_math_scores
})

# Minor data wrangling
math_scores_by_grade.index.name = None

# Display the DataFrame
math_scores_by_grade

# Use the code provided to separate the data by grade
ninth_graders = school_data_complete[(school_data_complete["grade"] == "9th")]
tenth_graders = school_data_complete[(school_data_complete["grade"] == "10th")]
eleventh_graders = school_data_complete[(school_data_complete["grade"] == "11th")]
twelfth_graders = school_data_complete[(school_data_complete["grade"] == "12th")]

# Group by `school_name` and take the mean of the `reading_score` column for each grade.
ninth_grade_reading_scores = ninth_graders.groupby('school_name')['reading_score'].mean()
tenth_grader_reading_scores = tenth_graders.groupby('school_name')['reading_score'].mean()
eleventh_grader_reading_scores = eleventh_graders.groupby('school_name')['reading_score'].mean()
twelfth_grader_reading_scores = twelfth_graders.groupby('school_name')['reading_score'].mean()

# Combine each of the scores above into a single DataFrame called `reading_scores_by_grade`
reading_scores_by_grade = pd.DataFrame({
    "9th": ninth_grade_reading_scores,
    "10th": tenth_grader_reading_scores,
    "11th": eleventh_grader_reading_scores,
    "12th": twelfth_grader_reading_scores
})

# Minor data wrangling
reading_scores_by_grade = reading_scores_by_grade[["9th", "10th", "11th", "12th"]]
reading_scores_by_grade.index.name = None

# Display the DataFrame
reading_scores_by_grade

# Establish the bins 
spending_bins = [0, 585, 630, 645, 680]
labels = ["<$585", "$585-630", "$630-645", "$645-680"]

# Create a copy of the school summary since it has the "Per Student Budget" 
school_spending_df = per_school_summary.copy()

Had to format again. Otherwise code was from the module.
# Remove non-numeric characters from "Per Student Budget"
school_spending_df["Per Student Budget"] = school_spending_df["Per Student Budget"].replace('[\$,]', '', regex=True)

# Convert "Per Student Budget" to numeric
school_spending_df["Per Student Budget"] = pd.to_numeric(school_spending_df["Per Student Budget"])

# Use pd.cut to categorize spending based on the bins
school_spending_df["Spending Ranges (Per Student)"] = pd.cut(
    school_spending_df["Per Student Budget"], 
    spending_bins, 
    labels=labels
)
print(school_spending_df)

Code from module
#  Calculate averages for the desired columns. 
spending_math_scores = school_spending_df.groupby(["Spending Ranges (Per Student)"])["Average Math Score"].mean()
spending_reading_scores = school_spending_df.groupby(["Spending Ranges (Per Student)"])["Average Reading Score"].mean()
spending_passing_math = school_spending_df.groupby(["Spending Ranges (Per Student)"])["% Passing Math"].mean()
spending_passing_reading = school_spending_df.groupby(["Spending Ranges (Per Student)"])["% Passing Reading"].mean()
overall_passing_spending = school_spending_df.groupby(["Spending Ranges (Per Student)"])["% Overall Passing"].mean()

# Assemble into DataFrame
spending_summary = pd.DataFrame({
    "Average Math Score": spending_math_scores,
    "Average Reading Score": spending_reading_scores,
    "% Passing Math": spending_passing_math,
    "% Passing Reading": spending_passing_reading,
    "% Overall Passing": overall_passing_spending
})

The cell errored. So I searched for a way to make it work. https://note.nkmk.me/en/python-pandas-nan-none-na/#:~:text=In%20pandas%2C%20a%20missing%20value,also%20considered%20a%20missing%20value.
# Get rid of NaN
spending_summary = spending_summary.fillna(0)

# Display results
spending_summary

I had to look this up. https://pandas.pydata.org/docs/reference/api/pandas.cut.html
# Categorize the spending based on the bins
# Use `pd.cut` on the "Total Students" column of the `per_school_summary` DataFrame.

per_school_summary["School Size"] = pd.cut(per_school_summary["Total Students"], size_bins, labels=labels)
per_school_summary

Code provided
# Calculate averages for the desired columns. 
size_math_scores = per_school_summary.groupby(["School Size"])["Average Math Score"].mean()
size_reading_scores = per_school_summary.groupby(["School Size"])["Average Reading Score"].mean()
size_passing_math = per_school_summary.groupby(["School Size"])["% Passing Math"].mean()
size_passing_reading = per_school_summary.groupby(["School Size"])["% Passing Reading"].mean()
size_overall_passing = per_school_summary.groupby(["School Size"])["% Overall Passing"].mean()

Made dataframe with new columns as before
# Create a DataFrame called `size_summary` that breaks down school performance based on school size (small, medium, or large).
# Use the scores above to create a new DataFrame called `size_summary`
size_summary = pd.DataFrame({
    "Average Math Score": size_math_scores,
    "Average Reading Score": size_reading_scores,
    "% Passing Math": size_passing_math,
    "% Passing Reading": size_passing_reading,
    "% Overall Passing": size_overall_passing
})

# Display results
size_summary

I merged here. Merging was done in class for an exercise. The per_school_summary didn't have size in it so I merged the original dataframe to it.
# Merging because the per_school_summary doesn't have the column requested

# Merge per_school_summary and school_data_complete on school_name
merged_per_school_summary_df = pd.merge(per_school_summary, school_data_complete, on="school_name")

# Rename column so I don't have to change it later
merged_per_school_summary_df = merged_per_school_summary_df.rename(columns={'type': 'School Type'})

# Display the merged DataFrame
merged_per_school_summary_df.head()

Code provided but updated for the merged dataframe
# Group the per_school_summary DataFrame by "School Type" and average the results.
average_math_score_by_type = merged_per_school_summary_df.groupby(["School Type"])["Average Math Score"].mean()
average_reading_score_by_type = merged_per_school_summary_df.groupby(["School Type"])["Average Reading Score"].mean()
average_percent_passing_math_by_type = merged_per_school_summary_df.groupby(["School Type"])["% Passing Math"].mean()
average_percent_passing_reading_by_type = merged_per_school_summary_df.groupby(["School Type"])["% Passing Reading"].mean()
average_percent_overall_passing_by_type = merged_per_school_summary_df.groupby(["School Type"])["% Overall Passing"].mean()

print(merged_per_school_summary_df.columns)

Made another dataframe with the new columns
# Assemble the new data by type into a DataFrame called type_summary
type_summary = pd.DataFrame({
    "Average Math Score": average_math_score_by_type,
    "Average Reading Score": average_reading_score_by_type,
    "% Passing Math": average_percent_passing_math_by_type,
    "% Passing Reading": average_percent_passing_reading_by_type,
    "% Overall Passing": average_percent_overall_passing_by_type
})

# Display results
type_summary
