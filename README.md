# congressional-behavior

List up data &amp; methodology about tracking the change of legislator's congressional behavior

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

3. So let's track down the case where the recipeint is `legislator` only at this moment, e.g. `Rep. Kevin Brady`.
