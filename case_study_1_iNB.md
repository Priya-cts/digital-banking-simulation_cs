iNB Case Study

Case Study Page 1 of 6

**Banking Project**

Indian Net Bank (iNB)

Case Study iNB Case Study

Case Study Page 2 of 6

**Table of Contents**

1\. Introduction ............................................................................................................................. 3

1.1. Background .................................................................................................................... 3

1.2. Overview ........................................................................................................................ 3

1.3. Acronyms and Abbreviations ......................................................................................... 3

2\. Requirements .......................................................................................................................... 4

2.1. Customer Registration ................................................................................................... 4

2.2. Home Page .................................................................................................................... 4

2.3. Accounts ......................................................................................................................... 4

2.4. Cheque Deposits ............................................................................................................ 4

2.5. Bill Payments .................................................................................................................. 5

2.6. Money Transfer .............................................................................................................. 5

2.7. Look-and-feel ................................................................................................................. 5

2.8. Security .......................................................................................................................... 5

3\. Data Fields .............................................................................................................................. 6

3.1. Customer ........................................................................................................................ 6 iNB Case Study

Case Study Page 3 of 6

**1\. Introduction**

**1.1. Background**

The purpose of this case study is to provide a implementation guide for developers.

This is to achieve a easy transition from ASP to ASP.Net projects.

**1.2. Overview**

The case study is for an _Online Banking Application_ for a fictitious bank named **Indian Net Bank (iNB)**. The proposed application must have some basic features for customers to perform their day-to-day banking operations over the Internet.

Apart from day-to-day operations, the site must also take into consideration security, ease of use, familiar look-and-feel across the application, easy navigation and performance.

Application will also have to be deployed and tested on IIS.

| Acronyms and Abbreviations iNB | Indian Net Bank |
| --- | --- |
| SDLC | Software Development Life Cycle |
| SET | Software Engineer Trainees |

**2\. Requirements**

**2.1. Customer Registration**

The customer should be able to register online to open an account with iNB. During registration, the customer will specify the type of account he wants to open. The registration request will be sent to the bank for an approval. The customer will be registered & the account will be created by the bank after the approval. The bank will send a letter to the customer once the registration is done. The customer should be able to logon to the site only after that. Customer can either have a Savings account or a Current account.

**2.2. Home Page**

The customer’s home page should be displayed after login. The home page should display the customer’s account information with balance.

**2.3. Accounts**

Customer can have two types of accounts; viz; Savings and Current. Customer should be able to see a Mini (last 5 transactions) & a Detailed (Date range) statement for an account.

**2.3.1. Savings Account**

Savings account, in addition to other basic information, will have a rate of interest, which customers can earn on their monthly balances.

All savings accounts in the bank will have the same rate of interest. If the rate changes, it will be effective for all savings accounts.

Customers should not be able to withdraw more than a configured amount on a day. A minimum balance must be maintained at all times. This amount must be configurable.

**2.3.2. Current Account**

Current accounts will not have a rate of interest as customers cannot earn interest on a current account.

A current account can have the facility of having a zero balance.

Customers should be able to avail of overdraft facility on their current accounts. If overdraft facility is availed, then the bank will charge a rate of interest on the overdraft amount to the customer. This rate of interest will be same for all current accounts, irrespective of the amount being withdrawn as overdraft.

**Overdraft Charges Calculation**

Overdraft charges will be calculated daily. So at the end of the day, if the balance is negative, overdraft charges will be added to the balance, resulting in the balance going down further in negative territory.

**2.4. Cheque Deposits**

The customer can deposit cheques issued against his name by filling online bank slip(s). After filling in the details, the customer can print & attach the slips to the cheques. The cheques along with the bank slips will be sent to the bank by post/courier. iNB Case Study

Case Study Page 5 of 6

The banker will see a list of all the online slips submitted by all the customers. By default, the status of cheques will be ‘Not received’. Once the cheques are received by the bank & sent for clearance, the status will be changed to ‘Sent for Clearance’. Cheques are usually cleared in 3 working days. The status of all the cheques that are cleared is changed to ‘Cleared’ & the customer’s account gets updated. If a cheque bounces, the status is changed to ‘Bounced’. Once a cheque bounces, a fine amount is deducted from the customer’s account. A bounced cheque will have to be resubmitted for clearance.

The customer should be able to check the status of all the deposited cheques online.

**Reconciliation**

A report will be printed as on a particular date, which will list all the cheques that have been received, and out of these received cheques, how many have been cleared, bounced and not-cleared.

**2.5. Bill Payments**

Customer can make online payments to utility services like electricity, telephone, cell phone etc. from his account. Bills can be paid immediately or they can be scheduled for payment at a later date.

**2.6. Money Transfer**

Customer can also transfer money to any iNB user account.

**2.7. Look-and-feel**

All pages must have the same look-and-feel with exactly the same header, footer and navigation panel on the left.

Users must be able to go to any screen with minimum mouse-clicks.

**2.8. Security**

After three consecutive invalid logon attempts, a user’s login account should be locked. This login account can only be re-activated manually.

**3\. Data Fields**

| 3.1. Customer Field | Mandatory | Comments |
| --- | --- | --- |
| First Name | Y |
| Last Name | Y |
| Username | Y | Alphabets only. Case in-sensitive. |
| Password | Y | Alphanumeric (a-z A-Z 0-9 ! @ # $ % ^ & *). Maximum 8 characters. Minimum 6 characters. Case sensitive. |
| Address Line 1 | Y |
| Address Line 2 |
| Address Line 3 |
| City | Y |
| State | Y |
| Zip | Y |
| Phone | Y | Format: 9999-9999 |
| Cell | Format: 99999-99999 |
| Email | Y | Format: xxx@yyy.com, xxx@yyy.co.in etc. |
| Customer Id | System-generated. |
