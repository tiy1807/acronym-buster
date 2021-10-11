# acronym-buster
Instructions on how to create a simple acronym buster.

These instructions give you the basic principles for explaining acronyms and other jargon. These terms can often be confusing to new joiners to your team, or indeed people who've been around a while.

This is all based within the standard Microsoft 365 suite of products.

You'll need a method for:

* storing your data
* reading an input
* looking up the acronym
* displaying the output

## Data storage

In order to automate, the data needs to be stored in a machine-readable format. This means it needs to be in structured data.

In a Microsoft 365 context, the easiest way is a SharePoint list.

The columns of our list are going to be:

* Title - contains the expanded term, such as Three Letter Acronym
* Acronym - contains the acronym, such as TLA
* Explanation - contains a few setences explaining the acronym
* Further Info - contains a link to more information

1. Open SharePoint.
2. If necessary, create a new site. (Usually public is best)
3. Go to the home of your SharePoint site.
4. Select new.
5. Select list.
6. Select blank list.
7. In name, enter Glossary. (or whatever you wish to call your acronym list)
8. In description, enter a short description of what your list is about
9. Select the settings cog icon in the top right.
10. Select list settings.
11. Select create column.
12. In column name, enter Acronym.
13. Select OK.
14. Select create column.
15. In column name, enter Explanation.
16. In description, enter A couple of sentence explanation. Try to assume as little knowledge as possible.
17. In Specify the type of test to allow, select Plain text.
18. Select OK.
19. Select create column.
20. In column name, enter Further Info.
21. In the type of information in this column is, select Hyperlink or Picture.
22. Select OK.

You now should be able to add information to your list, by selecting New. Each row is known as a SharePoint item.

## Reading an input

Once the data is stored in a format, the automated process needs to be triggered by some event.

In a Microsoft 365 context, you should use Power Automate.

1. Go to Power Automate.
2. Select Create.
3. Select Automated cloud flow.

You can now see all of the possible triggers that you could use. These could include when

* you recieve an email
* there's a new post to a particular Microsoft Teams channel
* you send a message to a Microsoft Power Virtual Agent
* a new response is submitted to a Microsoft Form

Depending on the input, you may need to clean it before trying to look up the acronym. For example, for Microsoft Teams messages, you could remove words like:

* what
* does
* mean
* ?

## Looking up the acronym

Independently of the input and output method, you need a way to take the acronym and look it up in your data.

In a Microsoft 365 context, you could use flow in Power Automate. To do this, you need to:

### Get the acronyms
1. Select new step.
2. Enter get items.
3. Select the get items SharePoint action.
4. In Site Address, select the SharePoint site to which your SharePoint list belongs.
5. In List Name, select the SharePoint list where your acronyms are stored.
6. In Filter Query, enter Acronym eq *acronymn*.
7. After the get items action, select new step.
8. Enter initialize variable.
9. Select the initialize variable action.
10. In Name, enter response.
11. In Type, enter string.
12. After the initialize variable action, select new step.

### For each matching acronym
14. Enter apply to each.
15. Select the Apply to each control action.
16. In select an output from previous steps, select the SharePoint output called value.
17. Within the apply to each action, select add an action.
18. Enter set variable.
19. Select the set variable action.
20. In Name, enter response.
21. In Value, enter the dynamic SharePoint variable Acronym, then ' stands for ', then the dynamic SharePoint variable Title.

### Add in explanation to the response
22. After the set variable action, within the apply to each action, select add an action.
23. Enter condition.
24. Select the condition control action.
25. In the left hand box, enter the dynamic SharePoint variable Explanation.
26. In the centre box, enter is equal to.
27. In the right hand box, enter the expression null.
28. In the If no branch, select add an action.
29. Enter append to string variable.
30. Select the append to string variable variable action.
31. In Name, enter response.
32. In Value, enter Explanation:, followed by the dynamic SharePoint variable Explanation.

### Add in further information to the response
33. Select the condition control action.
34. In the left hand box, enter the dynamic SharePoint variable Further Information.
35. In the centre box, enter is equal to.
36. In the right hand box, enter the expression null.
37. In the If no branch, select add an action.
38. Enter append to string variable.
39. Select the append to string variable variable action.
40. In Name, enter response.
41. In Value, enter Further info:, followed by the dynamic SharePoint variable Further Info.

The apply to each step means that if there are multiple matches all of them are shown to the user.

## Displaying the output

You will need to use the dynamic variable Response in your output. This could be one or more of:

* an email - use the Send an email Microsoft Outlook action
* a comment on Microsoft Teams - use the Post a reply to a message Microsoft Teams action
* a response in a Microsoft Power Virtual Agent

## Extensions

### Check the Title field
If no matching acronym has been found, you may wish to check the term (or Title) field.

To do this you need to use the same logic as before. You can copy the apply to each action by selecting the 3 dots, and then Copy to my clipboard. This then appears when you add new step. You just need to:

1. Add a condition to the end of the above, if response is equal to (then literally an empty box).
2. Amend the Filter Query step in the get acronyms instructions to use the Title field, rather than the Acronym field.

### Make case-insensitive
To make the acronym buster case insenstive you should amend the Filter Query step in the get acronyms instructions to `Acronym eq toLower(*acronym*) or Acronym eq toUpper(*acronym*)`.
