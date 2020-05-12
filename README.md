# congressional-behavior

List up data &amp; methodology about tracking the change of legislator's congressional behavior

# Nominated to the committee
# Switching party

## What Needs To be Done

- Classify the legislative behavior
  - sponsoring the bill
  - co-sponsoring the bill
  - 
- Check how the PAC contribution changes each behavior
  - Check how the `amount` affects the legislative action's degree

## Backgrounds
### Legislative Process
1. Representative(house) sponsors a billl
2. The bill is then assigned to a committee
3. The committee release the bill 
4. The bill is put on a calendar to be 
  - voted on
  - debated
  - amended
5. 

## Tracking
1. Let's start from the largest value contributed by the `PACs` 
```sql
select cd.organization_name, cb.contributor_name, p.pac_name, cb.payee_name, cb.amount, cb.recipient_name, cb.contribution_date 
from relational___campaign.contributiondisclosure cd
	inner join relational___campaign.contributions cb using ("ld203_uuid")
	inner join relational___campaign.pacs p using ("ld203_uuid")
where cb.amount::numeric > 0
order by cb.amount desc
>>>Boeing Company	Self	The Boeing Company Political Action Committee	Barack Obama Foundation	$9,999,999.00	Barack Obama - Obama Presidential Center - NOTE - Contribution was $10,000,000.00.  The form will not accept $10,000,000.00 - - -  LRC staff said to record the highest amount the form will accept.
Goodwill Industries International, Inc.	Self		Hyatt; Washington, DC	$8,642,017.00	Jane Oates
BOEING COMPANY	Self	United Space Alliance Political Action Committee (USAPAC)	Washington International Trade Foundation	$7,000,000.00	Rep. Kevin Brady, Rep. Joseph Crowley.
BOEING COMPANY	Self	The Boeing Company Political Action Committee	Washington International Trade Foundation	$7,000,000.00	Rep. Kevin Brady, Rep. Joseph Crowley.
BOEING COMPANY	Self		Washington International Trade Foundation	$7,000,000.00	Rep. Kevin Brady, Rep. Joseph Crowley.
```

2. If the recipient is `non-profit-org`, e.g. `barack obama foundataion` or `workingNations-JanesOates`, it's hard to track more since they are prohibited to directly/indrectly be involved in the political campaigns.

3. So let's track down the case where the recipeint is `legislator` only at this moment, e.g. `Rep. Kevin Brady, Rep. Joseph Crowley.`.

4. So now we have checked those two people have got `7M` from Boeing. 
4-1. The report year and report period is `2009 YY`, and we can look up `LD2` that is correpondent to this period, registered by `BOEING` as a registrant. (Since in this ase, `BOEING` is the registrant, whse in-house lobbying firm controlled this money flow)
```sql
select r."_report_uuid", c."client_full_name", regist."registrant_full_name", i.issue_code, i.specific_issue_text, ic.issue_code_description, r.reporting_year, r.reporting_quarter_code
from consolidated_layer_reports.reports r 
	inner join consolidated_layer_reports.clients as c using ("_client_uuid" )
	inner join consolidated_layer_reports.registrants as regist using ("_registrant_uuid")
	inner join consolidated_layer_reports.issues as i using ("_report_uuid")
	inner join consolidated_layer_reports.issues_code__reference as ic using ("issue_code")
where regist."registrant_full_name" ILIKE '%BOEING COMPANY%' and reporting_year = 2009 and r.reporting_quarter_code similar to '3|4'
order by amount desc
>>> 8a428ff9-fb5a-55f0-867f-c07ab85ced1d	BOEING COMPANY	BOEING COMPANY	HCR	"H.R. 3200 and S1679,  America's Affordable Health Choices Act,  Healthcare Reform.
H.R. 1897 and S. 803,  Health Workforce Act of 2009,  Employee Health Care.
America's Healthy Future Act.
House Tri-Committee Health Reform."	Health Issues	2009	4
```

5. So now, every congress year starts in odd year, we can search 2009-2010's legislative action of `Rep. Kevin Brady, Rep. Joseph Crowley.`
