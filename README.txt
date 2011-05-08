tmcivi_reports:
	A module to add dynamic sorting and grouping to CiviCRM reports.
	Built for CiviCRM 3.1.x, lightly tested with success on 3.3.5.,
            untested on newer versions.
	Requires tmcivi module (http://github.com/twomice/tmcivi)
        See below for NOTES.

Copyright (C) 2010  Allen Shaw (http://github.com/twomice 
	or http://nswebsolutions.com)

This program is free software: you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation, either version 3 of the License, or
(at your option) any later version.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with this program.  If not, see <http://www.gnu.org/licenses/>.


================
NOTES:
================

--------------
OVERVIEW:
--------------
This module provides a framework for adding dynamic sorting and grouping to
CiviCRM reports, in the form of a new section in the Report Criteria form
labeled "Order by Columns".  As an example, the existing report named
"Constituent Report (Summary)" is modified to support sorting by Country,
Email, and Last Name / First Name.

--------------
BASIC USAGE:
--------------
To allow sorting and grouping in your reports, add the array key 'order_bys'
to any element of the report class's $_columns array (normally done in the
report class's __construct() method).  The value for this key is an array of
columns from the given table which should be offered for sorting and grouping.
Each column is represented by an array, keyed to the SQL column name, and having
as its value an array of properties; currently the only relevant property is
'title', which indicates the user-readable label for that column.

For an example, see the code in this module at class/CRM/Report/Form/Contact/Summary.php
and search for "order_bys".

--------------
REGARDING alterDisplay():
--------------
Note also that many CiviCRM reports use the alterDisplay() method to place
numeric IDs with text values, and don't actually include those tables in the SQL
FROM clause. In this case, attempting to sort by those columns will generate an SQL
error.  For example, CRM/Report/Form/Contact/Summary.php is originally written
to select only the country_id column from the civicrm_address table, and then
to use alterDisplay() to replace country_id with a country name for each row, so
attempting to sort by civicrm_country.name will generate an SQL error.

In this case, to sort by civicrm_country.name, you need to include civicrm_country
in the $_columns array, and join the table into the FROM clause in the form()
method. For an example, compare this module's class/CRM/Report/Form/Contact/Summary.php
code with CiviCRM's original CRM/Report/Form/Contact/Summary.php code, specifically
in the __construct() and from() methods.

--------------
PERFORMANCE ISSUES:
--------------
Please be aware that attempting to sort the report by columns that are not indexed
can create significant performance issues. In CiviCRM, searchable custom fields
are indexed. You may want to check your CiviCRM database schema to see what
other columns are indexed. 
