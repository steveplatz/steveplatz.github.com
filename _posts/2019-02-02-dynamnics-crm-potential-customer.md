---
layout: post
title: Dynamics CRM Potential Customer
tags: dynamics dynamics365 opportunity crm 
description: Unexpected behavior when using the potential customer field in Dynamics CRM
keywords: dynamics dynamics365 crm opportunity potential-customer
---

This post describes an odd behavior of the Potential Customer Opportunity field that I recently ran across. I couldn’t find this documented anywhere else, so I wanted to share. Everything was tested on Dynamics CRM Online 9.0.1.1121.

## TL;DR

The Potential Customer field works differently depending on how an opportunity is created. If you manually click the New button or qualify a lead to an opportunity, the Potential Customer field will contain the associated account (so long as that field is populated). However, if you create a new opportunity from an existing Contact record, the Potential Customer will be the Contact, even when an Account has been specified.

## The Expected Behavior

Normally, when you create an opportunity, the Potential Customer is populated like so:

{:.table}
| Account (parentaccountid) | Contact (parentcontactid) | Potential Customer (customerid) |
|---------------------------|---------------------------|--------------------------------|
| Not Null                  | Null                      | Account                     |
| Null                      | Not Null                  | Contact                     |
| Not Null                  | Not Null                  | Account                     |

The account value is the primary value mapped to Potential Customer and it will always be mapped so long as it is not null, regardless of whether it was set at creation time or later on.

## The Unexpected Behavior

When creating an opportunity from an existing Contact, the Potential Customer field is always mapped to the Contact on creation of the Opportunity. 

{:.table}
| Account (parentaccountid) | Contact (parentcontactid) | Potential Customer (customerid) |
|---------------------------|---------------------------|--------------------------------|
| Null                      | Not Null                  | Contact                     |
| Not Null                  | Not Null                  | Contact                     |

<p class="table-caption">Null contact not possible, since opportunity is created from the context of a Contact</p>

Here’s what this looks like in action:

1. Open an existing Contact
2. Click the + button on the related opportunities grid to add a new opportunity.

    ![Create New Opportunity](/images/dynamics-crm-potential-customer/create-new-opportunity.png "Create New Opportunity")

3. Fill out the required fields and click Save.

    ![Opportunity Quick Create](/images/dynamics-crm-potential-customer/opportunity-quick-create.png "Opportunity Quick Create")

4. Navigate to the Opportunities List (Sales > Opportunities) and notice that the Potential Customer lists the Contact name, not the Account name.

    ![Potential Customer Contact](/images/dynamics-crm-potential-customer/potential-customer-contact.png "Potential Customer Contact")

As I mentioned earlier, I could find no mention of this behavior anywhere online or in Microsoft’s documentation. I’m unsure if this is the expected behavior or if this is a bug, but I can say this caught me a little off guard since it differs from how the field works in other instances.

## Our Workaround

A fix is relatively straightforward, because we always want the Potential Customer to reference the associated account. 

1. Create a new real-time workflow that executes on creation of an opportunity.
2. In the workflow definition, check if the Account has been specified and if so, whether it’s equal to the Potential Customer field. If it isn’t, then set the Potential Customer field equal to the account.

The end result ends up looking like this:

![Workflow Definition](/images/dynamics-crm-potential-customer/workflow-definition.png "Workflow Definition")

The nice thing about this solution is that it gives us the behavior we’re looking for when a new opportunity is created and the default behavior of the account field on the opportunity, described above, will take care of populating the Potential Customer field if the account changes in the future.