# alma_citation_inventory_analytics_join
We're going to use a `LEFT OUTER JOIN` to combine reports from the Courses and Physical Items subject areas.

We'll use this syntax:
`SELECT
   query_fields
FROM (first_query) A
LEFT OUTER JOIN (second_query) B
ON A.linking_field = B.linking_field`

### Step 1: Create Reports to Generate SQL
Start by creating an Analyses in the Course Reserves subject area with the following columns:
`Course Code` `Course Instructor` `Current Course End Date` `Reading List Code` `Reading List Due Back Date` `MMS Id` `Citation Id`

Add the following filters to the analyses:
- `Citation Id` is not null
- `Course Code` is not equal to / is not in NULL; EXLIBRIS_DEFAULT_COURSE
- `MMS Id` is not equal to / is not in -1

Save the analyses. Navigate to the Advanced tab, then to the SQL Issued Section. Grab the SQL code, excluding where it says ORDER BY. You should be left with:
`SELECT
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
(("Reading List Citations"."Citation Id" IS NOT NULL) AND ("Courses"."Course Code" NOT IN ('NULL', 'EXLIBRIS_DEFAULT_COURSE')) AND ("Bibliographic Details"."MMS Id" <> '-1'))`
