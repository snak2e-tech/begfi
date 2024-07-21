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
   - If BORROWER is verified:
	   - Begfi displays "You can Request Loans" box prompt 
	   - and the Borrower Message Request Button (on the top of the landing page) goes from gray to orange. 

### Borrower Posts Request

7. The Borrower Request button is a form to begin a Borrow Request follows a form where users can only enter certain information. 
8. In the SQL database, when user enters the following details for the loan request:
	- Amount requested (`$AMOUNTREQUEST`)
		- Amount of money being requested is a numerical value
	- Repayment time (`$TIMEinDAYS`)
		- Number of days until repayment is a numerical value of days
	2. Total amount to be repaid (`$AMOUNT2PAY`)
		- Amount to be paid back is a numerical value
	3. The reason why is a multiple choice option of different reasons in a dropdown.
9. Below their profile UPON posting the request will be their loan information history (described later in the Loanbot info) so lenders can gauge the borrower's credit worthiness

10. **User History (Existing Borrowers):**
   - Upon submitting the request, the system:
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
  -  Examples here of to implement: [https://redditloans.com/js/components/money.js](https://redditloans.com/js/components/money.js)
	  - view-source:[https://redditloans.com/loan_stats.html](https://redditloans.com/loan_stats.html)
	  - [https://redditloans.com/loans.html](https://redditloans.com/loans.html)

## Lender Response

9. **Lender Notification:** 
 Front-end website from UI/UX perspective:
   - The system identifies potential lenders and notifies them about the loan request.
	   - Website can filter:
		   - most recent borrower requests
		   - borrower requests by size
		   - first-time borrower requests (because these get higher rewards when a lender successfully lends to them)
		   - this allows lenders to also scroll through and find people they want to lend to
   - The lender can also see in the message board new borrower requests by just scrolling

11. **Lender Decision:**
   - Lender looks at a borrower's request
   - Once again the request looks like BORROWER needs to borrow "X" amount of money for "Y" days and will payback "Y" amount because of reason (from a multiple choice dropdown) "Z" reason.
   - There is a button to send borrower the loan under each username request on the message board called "Send Loan"

12. What is the Process after Lender accepts request and sends money?
	- Lender hits the button "Send Loan" 
		- This creates a prompt from Metamask to send that EXACT amount
	- After money is sent a box prompt tells Lender they have loaned an amount of money to the borrower, i.e. "Nice job! You have lent "X" username money"
	- Additional information can include:
		- Once the loan is paid off how many rewards the lender will receive
		- Lenders receive an additional 25 Beg tokens for a first-time borrower. Lenders receive 20 Beg tokens for second time-borrowers. Third-time borrowers means lenders get 15 Beg tokens. 
		-  Calculation follows: 
			'''class RewardsCalculator: def __init__(self): self.new_user_reward = 25 self.second_time_reward = 20 self.third_time_reward = 15 self.subsequent_rewards = 10 def calculate_loan_tokens(self, loan_amount): return loan_amount def calculate_user_rewards(self, borrow_count): if borrow_count == 1: return self.new_user_reward elif borrow_count == 2: return self.second_time_reward elif borrow_count == 3: return self.third_time_reward else: return self.subsequent_rewards def calculate_total_rewards(self, loan_amount, borrow_count): loan_tokens = self.calculate_loan_tokens(loan_amount) user_reward_tokens = self.calculate_user_rewards(borrow_count) return loan_tokens + user_reward_tokens'''
			- For every dollar of a loan, users receive one Beg token, where 1 Beg token equals 1 dollar. For example, a $100 loan results in 100 Beg tokens. 
			- For all subsequent loans after the third loan the lender makes, the reward is a flat rate of 10 Beg tokens.  
				- Example ''usage if __name__ == "__main__": loan_amount = 100  # Example loan amount borrow_count = 1   # Example borrow count (new user) calculator = RewardsCalculator() total_rewards = calculator.calculate_total_rewards(loan_amount, borrow_count) print(f"Total rewards for a loan of ${loan_amount} for a user with {borrow_count} borrow count: {total_rewards} Beg tokens")''' 
				- Prompt if they need a notification once the loan is repaid # Lender can put a messaging service like Telegram to inform them when loan is due.
				- Issue: lenders may not want to do notifications though. They're busy people. Likely, no one uses the notifications option.

## Lender Sends Loan (Smart Contract & Blockchain)

13. **Lender Send Loans:**
   - Upon confirmation, the lender submits a transaction via their wallet to approve the loan.
			   -  Money is sent using an actual box called "Send Loan"
			   - This leads to their metamask opening with the exact amount there and the lender approves and the money is sent to the borrower.
				   - Post-MVP, a stealth wallet method might be possible. But looking at the ecosystem, these seem very early in development. This would be a maximum safety feature.
	   - The QUERY process from the time the button for the Lender to push "Send Loan" occurs follows:
		-  After transaction goes through a contract takes the requested amount (`$AMOUNTREQUEST`) from the borrower's request on the Message Board.
			- The Graph opens a query to check the txn ID if the amount has been sent, the correct amount has been sent, and to the correct address.
				- Typically, projects like Tenderize use The Graph to check APYs (to calculate rewards on a time-average basis)
					- For example, projects sometimes run into problems depending on how they code something like an APY that takes he rewards needed to payout holders: https://www.tenderize.me/blog/the-graph-apy-calculation-post-mortem
					- "**Tenderize calculates the APY by querying our subgraph for the reward periods that happened in the last 30 days.**"
					- Therefore, think of The Graph as acting as an on-chain microservice, and calling the subgraph as doing a microservice for a particular part of process
			- How to Query: https://thegraph.com/docs/en/querying/querying-from-an-application/
			- Query examples: https://github.com/graphprotocol/query-examples
			- Tenderize, example: https://thegraph.com/explorer/subgraphs/2ygWAHMhm9tKXm59DN8DdSJ7VNpxrKMkA39Krij4b4tu?view=Query&chain=arbitrum-one
				- Tenderize calling the Graph and newNode`: Address of the new node to migrate to: 
					- https://github.com/Tenderize/tender-core/blob/06326ec2341da6ddf6269577e91eecf46c3d821b/contracts/tenderizer/integrations/graph/Graph.sol#L83`
14. After the Query, and the txn ID is confirmed to have gone to the right person, the SQL database is updated:
			- USERNAME ID of borrower
			- USERNAME ID of lender
			- Since the query has checked if the loan was the correct amount according to what the Lender requested, the SQL database updates:
				 - “Loan Sent"  
				   - "Not paid"  
				   - “Paid"
			- The front-end website also html changes the text in a box next to the borrower to the correct terms "- “Loan Sent" - "Not paid" - “Paid""

## More info on Transaction check via The Graph

15. **Blockchain Query:**
   - Begfi uses the blockchain's API to query the transaction and confirm if the funds (`$AMOUNTREQUEST`) have been transferred to the smart contract.
   - This query occurs across all loans every 30 seconds.
   - The cost of this for 5000 loans PER MONTH is $8
   - If such a situation where 1) total sum does not equal $75 (where the borrower has not paid in full) or has paid in batches so that you just look at all the transactions from person A to person B, between the time when the loan was given and the loan is due, and add the numbers up.
   - Create the query, in The Graph, [https://www.youtube.com/watch?v=EJ2em_QkQWU](https://www.youtube.com/watch?v=EJ2em_QkQWU) (doc video explaining how to create query) whereby it checks every 3 minutes if Lender's wallet has received Borrower's transfer from Borrower's wallet. And if it is in small batches, or the entire sum at once, the bot sums up and equals the correct amount and therefore creates a trigger:  
	   - “Loan Sent"  
	   - "Not paid"  
	   - “Paid"
- Finally, POST-MVP, In the rare instance that there’s a problem from the borrower side, then they can message Begfi team via a web3 messaging system widget that is available to borrowers only: https://dm3.network/build-with-dm3 whose mission is: 
	- "Build with dm3 Add web3 messaging to your dApp The dm3 protocol makes it easy to add messaging capabilities to a dApp. With just a few lines of code, you can integrate our customizable widget. This widget can be used for support chat, in-app communication, or any other type of messaging within the dApp. Whether you want to offer support, facilitate social interactions, or create any other type of in-app communication, the dm3 protocol can help you do it easily and quickly."

16. **SQL Entry:**
   - Upon successful transfer confirmation:
     - Update the loan status in the database (e.g., `loans` table) 
     - Record the loan details in the `loans` table with relevant columns like `borrower_id`, `lender_id`, `amount`, `time_to_repay`, `total_amount_to_pay`, and `status`.
## Lender is Paid Back 

17. Borrower sends back money.
	-  Prompt for sending it back exists
18. Notification at the end of the payment date via The Graph, API fetch if loan has been paid back by showing if address of borrower has sent money to address of lender
19. In order for this to work, the Borrower cannot simply send the money to the lender's address, i.e. copy the address of the lender and send money. Although, it would work and The Graph will still get the correct data... however, the box "Repay loan" should exist because:
	1. ***Neither the lender nor the borrower should see each other's addresses on message board... just a USERNAME***
				   - This prevents the borrower from mistakenly finding the address (extra info) and using another wallet and entering in the lender's address (because they now know it)
				   - Even though the borrower can see the address in the transaction, we have to assume that newer borrowers probably won't check the address in his wallet.
				   - Also, borrowers might mistake that as the thing they need to copy it.
				   - Or, the borrower's friend might use that address and send money on the borrower's behalf without the lender ever knowing about it
## Loan Bot to show Loan Information to Lender and its Functionality##

Basic reddit Loanbot documentation: 
- https://www.reddit.com/r/LoansBot/comments/2ea912/loansbot_basic_usage/
- https://botwiki.org/bot/loansbot/

- Bot should probably work by taking the total amount owed instead of the initial amount.
  - For example, if the bot would take only $50 but the lender gets repaid $75 USD, then it might be confusing. According to the GitHub documentation of /loanbot, 
	  - "The LoansBot does not consider interest:
		  - A loan is considered repaid when the entire principal has been repaid, regardless of any outstanding interest. This is also reflected on the ban status of users (in most circumstances). 
		  - So a $100 loan is considered fully repaid when the borrower has returned $100."
	  - Another way of looking at this is that interest amounts are not considered part of the info relevant to the LoansBot.
- All loans are in USD. This is not because we discriminate against other currencies, but almost all important stablecoin transactions occur in USD-related stablecoins.

## Reasons Why Begfi Should Succeed

Offering small amounts of credit to individuals who may not have access to traditional banking. However, the traditional finance (TradFi) space often falls short in terms of accessibility, speed, and cost-effectiveness. Begfi, leveraging the power of blockchain technology and Web3 verification, aims to revolutionize microloans. This document outlines why crypto-based microloans are superior, the importance of Web3 verification, and the potenti

A.  **Advantages Over Traditional Platforms:**
   - Unlike LendingClub (down over 95% in stock price), LendingTree, and Prosper, which are all platforms where you invest and they issue loans, these services don't involve peer-to-peer lending. Instead, they act as intermediaries for your transactions.
   - (Venmo/CashApp/ApplePay/PayPal) are more willing to shut down accounts they think are being used to do p2p loans.

B. **Market Trends:**
   - Compared to before, there is a larger base of customers for crypto-based loans worldwide.
   - In the developing world, higher interest rates mean higher bank loan requirement restrictions than ever (due to higher interest rates) that hurt low-income people from getting personal loans.
   - APRs for p2p lending are still very good compared to bonds in MOST countries (a 10% APR arbitrage opportunity).
   - APRs for p2p loans are still LOWER than most bank payday loans.

C. **Marketing Potential:**
   - It is Tiktokable. An influencer can be paid $500 to talk to their 100,000 followers to go to Begfi instead of a bank because bank loans are now 9% APR with a mortgage/house as collateral, no-collateral loans are 17%+, but they can still freeze your bank account and go after your assets by re-selling the loan to debt collection agencies.
