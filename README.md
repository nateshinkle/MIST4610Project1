
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
![Data Model](https://github.com/nateshinkle/MIST4610Project1/blob/main/DataModel.png)

# Data Dictionary

# Queries

    Query 1 

SELECT Client.fname, Client.lname, Loan.amount, Loan.interestRate, Loan.Term, Paymentschedule.Payment_Number, Paymenttransaction.amountPaid
FROM Client
JOIN Loan ON Client.idClient = Loan.Client_idClient
JOIN Paymentschedule ON Loan.idLoan = Paymentschedule.Loan_idLoan
JOIN Paymenttransaction ON Paymentschedule.idPaymentschedule = Paymenttransaction.Paymentschedule_idPaymentschedule
WHERE Loan.Status = 'Active';


    Query 2 

SELECT Client.fname,Client.lname, Client.email, Client.creditScore
FROM Client
WHERE Client.idClient IN (SELECT Loan.Client_idClient FROM Loan WHERE Loan.Status = 'Active');

    Query 3

SELECT Client.fname,Client.lname, (SELECT COUNT(*) FROM Loan WHERE Loan.Client_idClient = Client.idClient) AS Loan_Count
FROM Client
WHERE EXISTS (SELECT 1 FROM Credit_History WHERE Client.idClient = Credit_History.Client_idClient AND Credit_History.Defaults_History > 0);

    Query 4

SELECT Loan_Application.Branch_idBranch, COUNT(*) AS Total_Loans, SUM(Loan_Application.Requested_Amount) AS Total_Amount_Requested
FROM Loan_Application
GROUP BY Loan_Application.Branch_idBranch;

    Query 5

SELECT *
FROM Paymentschedule
WHERE Paymentschedule.dueDate > Current_date AND Paymentschedule.Status NOT IN ('Pending');

    Query 6

SELECT *
FROM Paymentschedule
WHERE Paymentschedule.dueDate > Current_date AND Paymentschedule.Status NOT IN ('Pending');
SELECT Loan.idLoan, DATEDIFF(CURRENT_DATE, Loan.startDate) AS Days_Active, ROUND((Loan.Amount * Loan.interestRate / 100), 2) AS Interest_Accrued
FROM Loan
WHERE Loan.Status = 'Active';

    Query 7

SELECT Employee.Name, Employee.Contact_Info
FROM Employee
WHERE Employee.Name REGEXP '^John';

    Query 8

SELECT Client.idClient, Client.fname, Client.lname
FROM Client
WHERE NOT EXISTS (SELECT 1 FROM Loan WHERE Loan.Client_idClient = Client.idClient);

    Query 9

SELECT YEAR(Loan_Agreement.Sign_Date) AS Year, COUNT(*) AS Total_Agreements, SUM(Collateral.Valuation_Amount) AS Total_Collateral_Value
FROM Loan_Agreement
JOIN Collateral ON Loan_Agreement.idLoanAgreement = Collateral.Loan_idLoan
GROUP BY YEAR(Loan_Agreement.Sign_Date);

    Query 10

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






