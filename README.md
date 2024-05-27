# HR Analytics AtliQ

## **1. Problem Statement**

In this project, we'll take a real dataset and see how a data analyst thinks and reflects. The data we'll use comes from AtliQ, a software and data solutions company. It will provide us with employee attendance data. We have three months of data. We have the dates in the columns and the employee names in the rows.

## **2. Understanding Requirement.**

In this section we'll use the data (Excel file) provided by AtliQ's HR manager and try to understand her needs.

As a data analyst, it's important that we listen more than you talk. In this particular phase, we need to keep the customer fully engaged by asking good questions. It's also an opportunity for us to become their strategic partner rather than just a solution provider.

Ask questions to understand their short- and long-term plans and align your solutions accordingly.

## **Insights They Need**
 * Working Preference of People From Home And From Office
 * Find Sick Leave %

If we know when most employees are working at home or in the office, we can easily plan activities for everyone.

## **3. Gathering and Transforming Data**
In this section, we'll talk about collecting and transforming data with Power Query. Power Query is a feature in Power BI that helps you collect and transform data.

- One of the tricks of the data analyst trade is Googling solutions.
- A parameter is a way to dynamically filter data.

1. Get a table with all the worksheets listed
2. Duplicate the query above and call the new query Template
3. Create a Template query
4. filter the `Apr 2022` sheet we're going to use as a parameter
5. Delete other columns and keep the column we will extend next
6. We'll carry out the various transformation steps
   * Promote the header,
   * delete the first row
   * rename the columns (Employee Code and Name): First two columns 
   * Rotate other columns except Employee Code and Name
   * Create a new parameter to be used for dynamic filtering
   * Create a `GetData` function on the Template query
   * Invoke this function on the other duplicate query named ( Final Data)
7. Apply modifications and transformations.

## **4. Creating metrics**

Metrics are quantitative assessments that help us make decisions.

1. People's preference between working at home and working in the office
2. Percentage of total sick leave to understand to monitor employee well-being
3. Learning the basics of Power BI will also be one of the constant goals of a data analyst when designing projects.


**We start by creating a new `Key Measures` table.**

1. Let's start looking for the total number of days worked:

   Here's the formula:

   > Total Working Days =
   > var totaldays = COUNT('Final Data'[value])
   > var nonworkingdays = CALCULATE(COUNT('Final Data'[value]), 'Final Data'[value] in {“WO”, “HO”})
   > RETURN
   > totaldays - nonworkingdays

We'll use `CALCULATE()` function to calculate WO(Weekly off) and Holidays(HO)

Let's continue by creating a new column ( WFH Count ) { WFH -> 1 and HWFH -> 0.5}.

Here's the formula:

> WFH Count = SWITCH(TRUE(),
> 'Final Data'[value] = “WFH”, 1,
> 'Final Data'[value] = “HWFH”,0.5,
> 0)

After that, we'll create a new metric measure 

> WFH Count = SUM('Final Data'[WFH Count])
>
> > Present Days =
> > var Presentdays = CALCULATE(COUNT('Final Data'[value]), 'Final Data'[value] = “P”)
> > RETURN
> > Presentdays + [WFH Count]


> Presence % = DIVIDE([Present Days], 'Key Measures'[Total Working Days],0)

To try to visualize our metrics by month, we'll try to create a new `Month` column

> Month = FORMAT('Final Data'[value], “mmmyy”); To change the format we'll use: (mmmyy)

Now we need to find the percentage of employees who worked from home. We'll just need to know the Work From Home percentage.

> WFH % = DIVIDE([WFH Count], [Present Days],0)
>
> > We divided by [Present Days] because we want to see how many of those present worked from home. For example, if a person is present 10 days and his WFH percentage is equal to 20%, this means that in the 10 days, 2 days he works at home.

** Now let's calculate the Metric Sick Leave % (SL %).

Let's continue by creating a new column ( SL Count ) { SL -> 1 and HSL -> 0.5}.

Here's the formula:

> SL Count = SWITCH(TRUE(),
> 'Final Data'[value] = “SL”, 1,
> 'Final Data'[value] = “HSL”,0.5,
> 0)

After that, we'll create a new metric measure 

> SL Count = SUM('Final Data'[SL Count])

- Let's create another measure in the `Key Measure` table [SL %].
- > SL % = DIVIDE([SL Count], [Total Working Days],0)

Now we'll need to group all the Metrics tops as <strong>(Presence %, WFH % and SL % )</strong>

We may want to have more information about a particular person. Get a more detailed visual overview using a matrix.

We'll create 3 stacked area Charts
* Presence % by Date
* WFH % by Date
* SL % by Date

After that, we'll apply two hard filters to the set of 3 charts.

1. Filter all Pages
      * value is not WO
2. Filter on this page
      * Filter type =  Advanced filtering
      * Show items when the value = is on or before 17/06/2022

Create a new column ( Days of the week )

> Days of Week = FORMAT('Final Data'[Date], 'ddd')

