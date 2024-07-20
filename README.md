# begfi Documentation

## Website Logic (Non-Smart Contract & SQL)

### User Onboarding (Borrower)

1. **Join Begfi:**
   - User creates an account on Begfi.
   - Connects wallet.
   - Creates username.

## Borrower Registration Process

### Initial Steps:
   - Borrower gets to the website.
   - Before registration and username account creation, they are prompted to connect wallet via Sign In With Ethereum (SIWE): [https://docs.login.xyz/](https://docs.login.xyz/)
   
2.  **Become a Borrower:**
   - User chooses the "Borrower" option. The borrower button is on the top of the page.
	   - New users are prompted with the question "Are you a Borrower" or are you a "Lender" and each box prompt has information like 
		- "- This is a no-money down, no collateral, micro-loans platform.
		- First-time users can borrow up to $20 for a maximum of two months.
		- Verification via CIVIC (civic.me) is required to request a loan.
		- Loans can be repaid early, allowing for higher borrowing limits next time.
		- In your request, promise a repayment amount to the lender (anyone on the website).
		- Small loans, like $20, often have higher fees; expect to repay around $24 or more.
		- Choose an interest rate that attracts lenders while being affordable for you.
		- Timely repayment is crucial; late or unpaid loans are reported to lenders, affecting future borrowing ability."
	- This is followed by a prompt box explains what that entails and tells them they need to verify via CIVIC."
	-Lenders do not have to do anything or verify. They get a box prompt that explains they are welcome to start loaning on the website, what verification processes borrowers go through, and what rewards they will get.

### Verification with Civic

3. **Verifies Wallet:**
   - Borrowers connect their wallet to Begfi again to verify their CIVIC pass.

4. **Wallet Check:**
   - Upon connecting the wallet, Begfi has a SQL entry to check if the wallet address is associated with a verified Civic identity in the `users` table (assuming a column `civic_id` exists).
   - **SQL Query (Verification):**

     ```sql
     SELECT civic_id
     FROM users
     WHERE wallet_address = ?;
     ```

5. **Verification Status:**
   
   - **At the end of The Box Prompt information to be Verified receive a box to go "Next" and the "Next" button takes them to Civic:**
   - Overlaid UI (over begfi) website after "Next button" of Civic over begfi
	   - https://docs.civic.com/integration-guides/civic-pass/integration-overview/ui-integration
	   - ![[ภาพถ่ายหน้าจอ 2024-07-20 เวลา 06.36.56.png]]
     - Prompt user to connect to Civic and verify their "UNIQUENESS" attribute. 
	     - This takes them from Begfi
     - **Civic Verification Documentation:**
       - UNIQUENESS verification: https://docs.civic.com/integration-guides/civic-pass/integration-overview/ui-integration
       - After implementing the UI modal overlay, we could also a secondary implementation to have under their profile a set of buttons that show their status as well![[ภาพถ่ายหน้าจอ 2024-07-20 เวลา 06.41.49.png]]
	       - import IdentityButton from '@civic/ethereum-gateway-react'; ... <IdentityButton /> in https://docs.civic.com/integration-guides/civic-pass/integration-overview/ui-integration/ui-modal

### Borrower On-boarding Verification Process:
   - After verifying their wallet, they return to Begfi and Begfi checks uniqueness via the GatewayProvider to ensure they verified via the uniqueness ID.
	   - a React component that enables your dApp frontend:
	     [https://docs.civic.com/integration-guides/civic-pass/integration-overview/ui-integration/ui-modal](https://docs.civic.com/integration-guides/civic-pass/integration-overview/ui-integration/ui-modal)
	     - `import { useGateway } from "@civic/ethereum-gateway-react";`
	     - `const { gatewayStatus, gatewayToken } = useGateway();`
   - Begfi has a network key to check uniqueness:
     - Get network key here for particular Civic passes: [https://docs.civic.com/integration-guides/civic-pass/available-networks](https://docs.civic.com/integration-guides/civic-pass/available-networks)
   - **Verified:** If `civic_id` is found, user is considered verified.
     - Display message: "Borrower Verified" (referencing Civic Pass documentation).
	   - Registration of network key requires submission to CIVIC.
   - Dashboard data for users available in plus plan from CIVIC. https://www.civic.com/pricing/
   - Badges on Borrower profile is updated to show they have "Active" Civic pass

### Borrower Can See "Need to Borrow" button and Message Board borrow request buttons

6. **Form Display:**
   - If verified, Begfi displays "You can Request Loans" box prompt and the Borrower Message Request Button and Form go from gray to orange. 

### Borrower Posts Request

7. The form to begin a Borrow Request follows a form where users can only enter certain information.
	1. Amount of money being requested is a numerical value
	2. Number of days until repayment is a numerical value of days
	3. Amount to be paid back is a numerical value
8. Below their profile will be their loan information history (described later in the Loanbot info)
9. **Request Details:**
   - In the SQL database, when user enters the following details for the loan request:
     - Amount requested (`$AMOUNTREQUEST`)
     - Repayment time (`$TIMEinDAYS`)
     - Total amount to be repaid (`$AMOUNT2PAY`)

10. **User History (Existing Borrowers):**
   - Upon submitting the request, the system (possibly using a background process):
     - Fetches the borrower's history from the database (assuming tables like `loans` and `borrower_history` exist).
     - Calculates statistics:
       - Number of loans paid off (`X total loans paid off, i.e. $1200 loans paid back`)
       - Number of outstanding loans (`Y amount of total loans outstanding, i.e. $50 loans outstanding`)
       - Total number of loans (`Z number of total loans. i.e. has received 4 loans`)
     - Displays the calculated user history.

## Loanbot Information Below Borrower Profile 

- Beside borrower profile is the following:
  - Loan bot API called if borrower has “X#” of loans, has “Y#” amount outstanding, “Z#” amount total paid off
  - Previous history of loans by borrowers
  - Previous history of loans paid back
  - Number of previous loans taken out
  - Number of loans paid back by lenders
  - [https://redditloans.com/js/components/money.js](https://redditloans.com/js/components/money.js)
  - view-source:[https://redditloans.com/loan_stats.html](https://redditloans.com/loan_stats.html)
  - [https://redditloans.com/loans.html](https://redditloans.com/loans.html)

## Lender Response

9. **Lender Notification:** 
   - The system identifies potential lenders and notifies them about the loan request.
   - The lender can also see in the message board new borrower requests

10. **Lender Decision:**
   - The lender decides whether to accept or reject the request.

11. Process after Lender accepts request and sends money
	1. After money is sent a box prompt tells Lender they have loaned an amount of money to the borrower
	2. Additional information can include:
		1. Once the loan is paid off how many rewards the lender will receive
		2. Prompt if they need a notification once the loan is repaid
			-  Some people may make so many loans that they do not want to be inundated with loan notifications.
			- Some people, in the future, post-MVP will want to have option to talk to the borrower and potentially extend a loan if needed
				- This would require a message system though

## Lender Accepts (Smart Contract & Blockchain)

13. **Lender Approval:**
   - Upon confirmation, the lender submits a transaction via their wallet to approve the loan.
   -  Money is sent
		-  The smart contract takes the requested amount (`$AMOUNTREQUEST`) from the borrower's request.
	- Information only includes the wallet address of who it was sent to (so we can fetch this info later for our loan bot)
- No BEG token rewards will be automatically distributed after the initial transferLender's message on Message board shows reply "Loan Sent"
   - Smart contract then leads to a blockchain query to confirm it went to the right person according the borrower ID and wallet address. 
	   - Once money is sent, wording on message board goes from "Borrower Request" to "Loan Disbursed"
	   - "Request" also goes to "Unpaid yet"
	   - When paid back it goes from "Loan paid back "Loan Repaid" and "Request" to "Paid"
## Blockchain Transaction & SQL Entry

14. **Blockchain Query:**
   - Begfi uses the blockchain's API to query the transaction and confirm if the funds (`$AMOUNTREQUEST`) have been transferred to the smart contract.

15. **SQL Entry:**
   - Upon successful transfer confirmation:
     - Update the loan status in the database (e.g., `loans` table) to "Funded."
     - Record the loan details in the `loans` table with relevant columns like `borrower_id`, `lender_id`, `amount`, `time_to_repay`, `total_amount_to_pay`, and `status`.
 
## The Graph to Check the Blockchain to Confirm SQL Entry##

16. **Cost**:
  - Documentation: [The Developer's Guide to the Web3 Stack](https://www.infura.io/blog/post/the-developers-guide-to-the-web3-stack)
  - The Graph, why? 
	  - Web3 app developers opt to pay usage-based query fees to indexers for blockchain data lookups, as this is far more cost-effective than managing the infrastructure themselves. 
	  - This approach leverages specialization and economies of scale, similar to the rationale behind most SaaS providers. 
	  - Indexers then distribute these fees among other backend participants, including delegators and curators.
17.  **What is a Subgraph**:
  - A subgraph extracts data from a blockchain, processes it, and stores it so that it can be easily queried via GraphQL.
18. The Graph **Fees**:
  - A basic query is estimated to cost $0.00001 each, however, this is ultimately set by indexers indexing your subgraph data. 
	  - **Compatibility**:
		  - Compatible with Solana, Polygon, and other chains.
  - Free 100,000 queries PER MONTH. https://thegraph.com/docs/en/billing/ 
	  - Imagine 1000 borrowers. 1000 lenders. This equals 2000. This is for each borrower/lender action. 
		  - Payment/lending TO is one action.
		  - Receiving funds and funds paid is one action.
	  - If begfi processes 1000 loans that are paid back, this means 2000 blockchain queries. 
	  - We would pay $.02 on the 5,100th loan that is paid back (because the first 100,000 queries PER MONTH are free) in one month.
		  - This means a potential loan volume averaging $5,000,000 and paying $.02 for it for The Graph fees
		  - Why is a The Graph, "Subgraph needed? 
			  - Indexers set the prices for their services, and the gateway allocates queries based on market conditions, technical parameters such as latency, and the preferences of the user making the request. 
			  - Currently, a standard cost model is configured at 0.00005 DAI per query, which aligns with the average pricing for the subgraphs being indexed and verified.
		  - GRT tokens need to be in Arbitrium.
			  - **How Paid**:
			  - [The Graph’s Billing System](https://thegraph.com/docs/en/arbitrum/arbitrum-faq/): "The Graph’s billing system accepts GRT on Arbitrum, and users will need ETH on Arbitrum to pay their gas. While The Graph protocol started on Ethereum Mainnet, all activity, including the billing contracts, is now on Arbitrum One."

## Lender is Paid Back 

19. Borrower sends back money.
	1. Prompt for sending it back exists
20. Notification at the end of the payment date via The Graph, API fetch if loan has been paid back by showing if address of borrower has sent money to address of lender
## Potential Problems

21. What happens if?
	1. Borrower does something wrong like
		- Their friend instead of them sends it back
		- They use a different wallet to send it back

## Loan Bot to show Loan Information to Lender and its Functionality##

Basic reddit Loanbot documentation: 
- https://www.reddit.com/r/LoansBot/comments/2ea912/loansbot_basic_usage/
- https://botwiki.org/bot/loansbot/

- Bot should probably work by taking the total amount owed instead of the initial amount.
  - For example, if the bot would take only $50 but the lender gets repaid $75 USD, then it might be confusing. According to the GitHub documentation of /loanbot, 
	  - "The LoansBot does not consider interest. A loan is considered repaid when the entire principal has been repaid, regardless of any outstanding interest. This is also reflected on the ban status of users (in most circumstances). 
	  - So a $100 loan is considered fully repaid when the borrower has returned $100. 
	  - Another way of looking at this is that interest amounts are not considered part of the info relevant to the LoansBot.
- All loans are in USD. This is not because we discriminate against other currencies, but almost all important stablecoin transactions occur in USD-related stablecoins.

## Additional Considerations

- **Security:** Implement robust security measures for user data, transactions, and smart contract interactions.
- **Error Handling:** Implement proper error handling mechanisms to gracefully handle unexpected situations. Probably would need someone to moderate the message boards and respond to issues via Discord. 

## Reasons Why Begfi Should Succeed

Offering small amounts of credit to individuals who may not have access to traditional banking. However, the traditional finance (TradFi) space often falls short in terms of accessibility, speed, and cost-effectiveness. Begfi, leveraging the power of blockchain technology and Web3 verification, aims to revolutionize microloans. This document outlines why crypto-based microloans are superior, the importance of Web3 verification, and the potenti

1. **Advantages Over Traditional Platforms:**
   - Unlike LendingClub (down over 95% in stock price), LendingTree, and Prosper, which are all platforms where you invest and they issue loans, these services don't involve peer-to-peer lending. Instead, they act as intermediaries for your transactions.
   - (Venmo/CashApp/ApplePay/PayPal) are more willing to shut down accounts they think are being used to do p2p loans.

2. **Market Trends:**
   - Compared to before, there is a larger base of customers for crypto-based loans worldwide.
   - In the developing world, higher interest rates mean higher bank loan requirement restrictions than ever (due to higher interest rates) that hurt low-income people from getting personal loans.
   - APRs for p2p lending are still very good compared to bonds in MOST countries (a 10% APR arbitrage opportunity).
   - APRs for p2p loans are still LOWER than most bank payday loans.

3. **Marketing Potential:**
   - It is Tiktokable. An influencer can be paid $500 to talk to their 100,000 followers to go to Begfi instead of a bank because bank loans are now 9% APR with a mortgage/house as collateral, no-collateral loans are 17%+, but they can still freeze your bank account and go after your assets by re-selling the loan to debt collection agencies.
