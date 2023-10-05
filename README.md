# alma_citation_inventory_analytics_join
We're going to use a `LEFT OUTER JOIN` to combine reports from the Courses and Physical Items subject areas.

We'll use this syntax:

<code>SELECT
   query_fields
FROM (first_query) A
LEFT OUTER JOIN (second_query) B
ON A.linking_field = B.linking_field</code>

If you don't want to go through every step and don't need to customize the report, skip to the last section. If you need additional filters, add them when creating the separate reports. You cannot customize filters once the reports are combined.
### Step 1: Create Reports to Generate SQL
#### Course Reserves Subject Area Analyses
Start by creating an Analyses in the Course Reserves subject area with the following columns:
`Course Code` `Course Instructor` `Current Course End Date` `Reading List Code` `Reading List Due Back Date` `MMS Id` `Citation Id`

Add the following filters to the analyses:
- `Citation Id` is not null
- `Course Code` is not equal to / is not in NULL; EXLIBRIS_DEFAULT_COURSE
- `MMS Id` is not equal to / is not in -1

Save the analyses. Navigate to the Advanced tab, then to the SQL Issued Section. Grab the SQL code, excluding where it says ORDER BY. You should be left with:

<code>SELECT
   0 s_0,
   "Course Reserves"."Bibliographic Details"."MMS Id" s_1,
   "Course Reserves"."Courses"."Course Code" s_2,
   "Course Reserves"."Courses"."Course Instructor" s_3,
   "Course Reserves"."Current Course End Date"."Current Course End Date" s_4,
   "Course Reserves"."Reading List Citations"."Citation Id" s_5,
   "Course Reserves"."Reading Lists"."Reading List Code" s_6,
   "Course Reserves"."Reading Lists"."Reading List Due Back Date" s_7
FROM "Course Reserves"
WHERE
(("Reading List Citations"."Citation Id" IS NOT NULL) AND ("Courses"."Course Code" NOT IN ('NULL', 'EXLIBRIS_DEFAULT_COURSE')) AND ("Bibliographic Details"."MMS Id" <> '-1'))</code>

#### Physical Items Subject Area Analyses
Create a Physical Items subject area analyses with the following columns:
`Barcode` `Temporary Item Policy` `Temporary Physical Location in Use` `Display Temporary Call Number` `Due Back Date` `Num of Items (In Repository)`
`Temporary Location Name` `MMS Id` `Title` `Author` `Edition` `Location Name`

Add the following filter to the analyses:
- Num of Items (In Repository) is not equal to / not in 0

Save the analyses. Navigate to the Advanced tab, then to the SQL Issued Section. Grab the SQL code, excluding where it says ORDER BY. You should be left with:

<code>SELECT
   0 s_0,
   "Physical Items"."Bibliographic Details"."Author" s_1,
   "Physical Items"."Bibliographic Details"."Edition" s_2,
   "Physical Items"."Bibliographic Details"."MMS Id" s_3,
   "Physical Items"."Bibliographic Details"."Title" s_4,
   "Physical Items"."Location"."Location Name" s_5,
   "Physical Items"."Physical Item Details"."Barcode" s_6,
   "Physical Items"."Physical Item Details"."Display Temporary Call Number" s_7,
   "Physical Items"."Physical Item Details"."Due Back Date" s_8,
   "Physical Items"."Physical Item Details"."Temporary Item Policy" s_9,
   "Physical Items"."Physical Item Details"."Temporary Physical Location In Use" s_10,
   "Physical Items"."Temporary Location"."Temporary Location Name" s_11,
   "Physical Items"."Physical Item Details"."Num of Items (In Repository)" s_12
FROM "Physical Items"
WHERE
("Physical Item Details"."Num of Items (In Repository)" <> 0)</code>

### Combining Analyses
Paste the Course Reserves SQL into the JOIN query at the `first_query`, and the Physical Items SQL into the `second_query`. You'll be left with this:

<code>SELECT
   query_fields
FROM (SELECT
   0 s_0,
   "Course Reserves"."Bibliographic Details"."MMS Id" s_1,
   "Course Reserves"."Courses"."Course Code" s_2,
   "Course Reserves"."Courses"."Course Instructor" s_3,
   "Course Reserves"."Current Course End Date"."Current Course End Date" s_4,
   "Course Reserves"."Reading List Citations"."Citation Id" s_5,
   "Course Reserves"."Reading Lists"."Reading List Code" s_6,
   "Course Reserves"."Reading Lists"."Reading List Due Back Date" s_7
FROM "Course Reserves"
WHERE
(("Reading List Citations"."Citation Id" IS NOT NULL) AND ("Courses"."Course Code" NOT IN ('NULL', 'EXLIBRIS_DEFAULT_COURSE')) AND ("Bibliographic Details"."MMS Id" <> '-1'))) A
LEFT OUTER JOIN (SELECT
   0 s_0,
   "Physical Items"."Bibliographic Details"."Author" s_1,
   "Physical Items"."Bibliographic Details"."Edition" s_2,
   "Physical Items"."Bibliographic Details"."MMS Id" s_3,
   "Physical Items"."Bibliographic Details"."Title" s_4,
   "Physical Items"."Location"."Location Name" s_5,
   "Physical Items"."Physical Item Details"."Barcode" s_6,
   "Physical Items"."Physical Item Details"."Display Temporary Call Number" s_7,
   "Physical Items"."Physical Item Details"."Due Back Date" s_8,
   "Physical Items"."Physical Item Details"."Temporary Item Policy" s_9,
   "Physical Items"."Physical Item Details"."Temporary Physical Location In Use" s_10,
   "Physical Items"."Temporary Location"."Temporary Location Name" s_11,
   "Physical Items"."Physical Item Details"."Num of Items (In Repository)" s_12
FROM "Physical Items"
WHERE
("Physical Item Details"."Num of Items (In Repository)" <> 0)) B
ON A.linking_field = B.linking_field</code>

Now we need to set up the fields that link these two analyses. We're going to use `MMS Id`. Note the SQL for the `MMS Id` for each subject area analyses:

`"Course Reserves"."Bibliographic Details"."MMS Id" s_1,`
`"Physical Items"."Bibliographic Details"."MMS Id" s_3,`

In our master syntax line `ON A.linking_field = B.linking_field`, we're going to pass in the relative position of the `MMS_Id` column in each report. We'll be left with:

`ON A.s_1 = B.s_3`

Now we need to select which fields are produced in the combined report and list them in the `query_fields`. For the purposes of this syntax, the Course Reserves analyses is `A` and the Physical Items analyses is `B`.

<code>A.s_1,
A.s_2,
A.s_3,
A.s_4,
A.s_6,
A.s_7,
B.s_1,
B.s_2,
B.s_4,
B.s_5,
B.s_6,
B.s_7,
B.s_8,
B.s_9,
B.s_10,
B.s_11,</code>

We're excluding columns `A.s_5 Citation Id`, `B.s_3 MMS Id`, and `B.s_12 Num of Items (In Repository)`

### Final SQL

<code>SELECT
A.s_1,
A.s_2,
A.s_3,
A.s_4,
A.s_6,
A.s_7,
B.s_1,
B.s_2,
B.s_4,
B.s_5,
B.s_6,
B.s_7,
B.s_8,
B.s_9,
B.s_10,
B.s_11,
FROM (SELECT
   0 s_0,
   "Course Reserves"."Bibliographic Details"."MMS Id" s_1,
   "Course Reserves"."Courses"."Course Code" s_2,
   "Course Reserves"."Courses"."Course Instructor" s_3,
   "Course Reserves"."Current Course End Date"."Current Course End Date" s_4,
   "Course Reserves"."Reading List Citations"."Citation Id" s_5,
   "Course Reserves"."Reading Lists"."Reading List Code" s_6,
   "Course Reserves"."Reading Lists"."Reading List Due Back Date" s_7
FROM "Course Reserves"
WHERE
(("Reading List Citations"."Citation Id" IS NOT NULL) AND ("Courses"."Course Code" NOT IN ('NULL', 'EXLIBRIS_DEFAULT_COURSE')) AND ("Bibliographic Details"."MMS Id" <> '-1'))) A
LEFT OUTER JOIN (SELECT
   0 s_0,
   "Physical Items"."Bibliographic Details"."Author" s_1,
   "Physical Items"."Bibliographic Details"."Edition" s_2,
   "Physical Items"."Bibliographic Details"."MMS Id" s_3,
   "Physical Items"."Bibliographic Details"."Title" s_4,
   "Physical Items"."Location"."Location Name" s_5,
   "Physical Items"."Physical Item Details"."Barcode" s_6,
   "Physical Items"."Physical Item Details"."Display Temporary Call Number" s_7,
   "Physical Items"."Physical Item Details"."Due Back Date" s_8,
   "Physical Items"."Physical Item Details"."Temporary Item Policy" s_9,
   "Physical Items"."Physical Item Details"."Temporary Physical Location In Use" s_10,
   "Physical Items"."Temporary Location"."Temporary Location Name" s_11,
   "Physical Items"."Physical Item Details"."Num of Items (In Repository)" s_12
FROM "Physical Items"
WHERE
("Physical Item Details"."Num of Items (In Repository)" <> 0)) B
ON A.s_1 = B.s_3</code>

#### Using the SQL

Open a new analyses in any subject area of Analytics. Navigate to the Advanced tab, scroll down, select New Analyses. Paste the SQL in. The report should populate. Customize the column names as desired and save the report. 





