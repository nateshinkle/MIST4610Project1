
# MIST 4610 Project 1
Project 1 for MIST 4610

# Group Shinkle Members:
Nathan Shinkle 

# Problem Description
The business model is built on the principle of accessibility and flexibility, providing personalized loan products that meet the unique needs of our clients. We leverage a detailed understanding of our clients' financial situations, which is encapsulated in our relational database through a range of entities that track everything from personal information and credit history to loan agreements and payment transactions.

# Data model
In the data model designed for the micro-loan provider, each table plays a pivotal role in capturing the multifaceted nature of the business operations. The Client table is fundamental in maintaining a database of personal and financial details of the borrowers, which is invaluable for customer service and loan assessment. The Loan table holds the core data of the lending process, including amounts, terms, and statuses, enabling the business to monitor its financial products and manage risk effectively. The Payment Schedule and Payment Transaction tables work in tandem to manage and track the inflow of repayments, providing insights into the financial health of the loan portfolio and the payment behavior of clients.

Employee and Branch tables are instrumental in managing the human and physical assets of the business, ensuring that resources are allocated efficiently and service delivery is optimized across different locations. The Loan Application table reflects the business's pipeline, capturing potential revenue and allowing for the evaluation of lending criteria and risk appetite. The Collateral table is critical for risk mitigation, ensuring that loans are backed by tangible assets.

The Loan Agreement table provides a legal foundation for the terms of each loan, protecting the business's interests and providing a framework for resolving disputes. The Account table facilitates financial transactions, ensuring that funds move securely and efficiently between the business and its clients. The Credit History table offers a snapshot of the borrower's financial reliability, informing lending decisions and interest rates. Lastly, the Loan Product table allows the business to diversify its offerings and strategize its product portfolio to meet market demand and enhance competitiveness.

Together, these tables form a comprehensive ecosystem that supports strategic decision-making, risk management, customer relationship management, and regulatory compliance, ultimately guiding the business towards sustainable growth and profitability.
![Data Model](https://github.com/nateshinkle/MIST4610Project1/blob/main/DataModel.PNG)

# Data Dictionary
Table: Account 
![Account](https://github.com/nateshinkle/MIST4610Project1/blob/main/Account.PNG)

Table: Client 
![Client](https://github.com/nateshinkle/MIST4610Project1/blob/main/Client.PNG)

Table: Collateral 
![Collateral](https://github.com/nateshinkle/MIST4610Project1/blob/main/Collateral.PNG)

Table: Credit_History 
![Credit_History](https://github.com/nateshinkle/MIST4610Project1/blob/main/Credit_History.PNG)

Table: Employee 
![Employee](https://github.com/nateshinkle/MIST4610Project1/blob/main/Employee.PNG)

Table: Loan 
![Loan](https://github.com/nateshinkle/MIST4610Project1/blob/main/Loan.PNG)

Table: Loan_Agreement 
![Loan_Agreement](https://github.com/nateshinkle/MIST4610Project1/blob/main/Loan_Agreement.PNG)

Table: Loan_Application 
![Loan_Application](https://github.com/nateshinkle/MIST4610Project1/blob/main/Loan_Application.PNG)

Table: Loan_Product 
![Loan_Product](https://github.com/nateshinkle/MIST4610Project1/blob/main/Loan_Product.PNG)

Table: Paymenttransaction 
![Paymenttransaction](https://github.com/nateshinkle/MIST4610Project1/blob/main/Paymenttransaction.PNG)

Table: branch 
![branch](https://github.com/nateshinkle/MIST4610Project1/blob/main/branch.PNG)

Table: Paymentschedule 
![Paymentschedule](https://github.com/nateshinkle/MIST4610Project1/blob/main/paymentschedule.PNG)
# Queries

    Query 1 
This query provides a comprehensive view of active loans, including client information, loan details, scheduled payment numbers, and amounts paid. It’s useful for monitoring current loans and payment progress.

SELECT Client.fname, Client.lname, Loan.amount, Loan.interestRate, Loan.Term, Paymentschedule.Payment_Number, Paymenttransaction.amountPaid
FROM Client
JOIN Loan ON Client.idClient = Loan.Client_idClient
JOIN Paymentschedule ON Loan.idLoan = Paymentschedule.Loan_idLoan
JOIN Paymenttransaction ON Paymentschedule.idPaymentschedule = Paymenttransaction.Paymentschedule_idPaymentschedule
WHERE Loan.Status = 'Active';


    Query 2 
This query fetches the names, emails, and credit scores of clients with delinquent loans. It's helpful for initiating collections or credit counseling processes.

SELECT Client.fname,Client.lname, Client.email, Client.creditScore
FROM Client
WHERE Client.idClient IN (SELECT Loan.Client_idClient FROM Loan WHERE Loan.Status = 'Active');

    Query 3
This query retrieves the names of clients and the count of loans they have, but only for those who have a history of defaults. This could be used for risk analysis and adjusting loan approval criteria.

SELECT Client.fname,Client.lname, (SELECT COUNT(*) FROM Loan WHERE Loan.Client_idClient = Client.idClient) AS Loan_Count
FROM Client
WHERE EXISTS (SELECT 1 FROM Credit_History WHERE Client.idClient = Credit_History.Client_idClient AND Credit_History.Defaults_History > 0);

    Query 4
This query groups loan applications by branch and calculates the total number of loans and the sum of the amounts requested per branch, which is useful for evaluating branch performance.

SELECT Loan_Application.Branch_idBranch, COUNT(*) AS Total_Loans, SUM(Loan_Application.Requested_Amount) AS Total_Amount_Requested
FROM Loan_Application
GROUP BY Loan_Application.Branch_idBranch;

    Query 5

SELECT *
FROM Paymentschedule
WHERE Paymentschedule.dueDate > Current_date AND Paymentschedule.Status NOT IN ('Pending');

    Query 6
This query selects all overdue payments that are not marked as paid or cancelled, aiding in the identification of pending collections.

SELECT *
FROM Paymentschedule
WHERE Paymentschedule.dueDate > Current_date AND Paymentschedule.Status NOT IN ('Pending');
SELECT Loan.idLoan, DATEDIFF(CURRENT_DATE, Loan.startDate) AS Days_Active, ROUND((Loan.Amount * Loan.interestRate / 100), 2) AS Interest_Accrued
FROM Loan
WHERE Loan.Status = 'Active';

    Query 7
This query returns the name of all employee's who's name starts with John.

SELECT Employee.Name, Employee.Contact_Info
FROM Employee
WHERE Employee.Name REGEXP '^John';

    Query 8
This query identifies clients who have not taken out a loan yet, which could be useful for targeting marketing efforts or customer outreach.

SELECT Client.idClient, Client.fname, Client.lname
FROM Client
WHERE NOT EXISTS (SELECT 1 FROM Loan WHERE Loan.Client_idClient = Client.idClient);

    Query 9
This query groups loan agreements by year, counts the total number of agreements, and sums the valuation amount of the associated collateral, providing a year-over-year comparison of lending secured by collateral.

SELECT YEAR(Loan_Agreement.Sign_Date) AS Year, COUNT(*) AS Total_Agreements, SUM(Collateral.Valuation_Amount) AS Total_Collateral_Value
FROM Loan_Agreement
JOIN Collateral ON Loan_Agreement.idLoanAgreement = Collateral.Loan_idLoan
GROUP BY YEAR(Loan_Agreement.Sign_Date);

    Query 10
This query aims to provide a branch-level summary of loan activity, focusing on high-credit clients (with scores above 600) and active loans initiated this year. By joining the loan_application table with both loan and client tables, we can derive the total loan amount per branch and the count of distinct clients who have taken loans. The subquery in the HAVING clause calculates 1.5 times the average loan amount issued since the beginning of the year, filtering for branches that exceed this figure, suggesting strong performance. This information is crucial for management as it highlights branches that are not only attracting creditworthy clients but are also disbursing higher loan volumes compared to the average, indicating potential areas for strategic investment or replication of best practices.

SELECT 
branch.idBranch, 
branch.address, 
COUNT(DISTINCT Client.idClient) AS NumberOfClients,
SUM(Loan.Amount) AS TotalLoanAmount
FROM 
branch
JOIN 
Loan_Application ON branch.idBranch = Loan_Application.branch_idBranch
JOIN 
Loan ON Loan_Application.idLoan = Loan.idLoan
JOIN 
Client ON Loan_Application.Client_idClient = Client.idClient
WHERE 
Loan.Status = 'Active'
AND Client.creditScore > 600
AND Loan.startDate >= '2023-01-01'
GROUP BY 
branch.idBranch
HAVING 
SUM(Loan.amount) > (SELECT AVG(Loan.amount) * 1.5 FROM Loan WHERE Loan.startDate >= '2023-01-01')
ORDER BY 
TotalLoanAmount DESC;






