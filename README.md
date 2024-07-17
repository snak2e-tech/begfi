# begfi

**Website Logic (Non-Smart Contract & SQL)**

**User Onboarding (Borrower)**

1. **Join Begfi:** User creates an account on Begfi. Connects wallet. Creates username. 
2. **Become a Borrower:** User chooses the "Borrower" option. Prompt box explains what that entails. Tells them they need to verify via CIVIC.
3. **Verifies Wallet:** Borrower connects their wallet to Begfi again to verify their CIVIC pass. 

**Verification with Civic**

4. **Wallet Check:** Upon connecting the wallet, Begfi has a SQL entry
5.
6. to check if the wallet address is associated with a verified Civic identity in the `users` table (assuming a column `civic_id` exists).

   - **SQL Query (Verification):**

     ```sql
     SELECT civic_id
     FROM users
     WHERE wallet_address = ?;
     ```

7. **Verification Status:**
   - **Verified:** If `civic_id` is found, user is considered verified.
     - Display message: "Borrower Verified" (referencing Civic Pass documentation).
   - **Not Verified:**
     - Prompt user to connect to Civic and verify their "UNIQUENESS" attribute.
     - Provide a link to the relevant Civic verification documentation.

     **Civic Verification Documentation:**

       - UNIQUENESS verification: [https://civic.me/7KG82MeFgB169KLDa9uc91XLDHMah7MLiBfzi8VfviwH](https://civic.me/7KG82MeFgB169KLDa9uc91XLDHMah7MLiBfzi8VfviwH)
       - Integration overview: [https://docs.civic.com/integration-guides/civic-pass/integration-overview/ui-integration/pass-status-ui](https://docs.civic.com/integration-guides/civic-pass/integration-overview/ui-integration/pass-status-ui)

**Borrower Request Form**

6. **Form Display:** If verified, Begfi displays the Borrower Message Request Form.

**Borrower Posts Request**

7. **Request Details:** User enters the following details for the loan request:
   - Amount requested (`$AMOUNTREQUEST`)
   - Repayment time (`$TIMEinDAYS`)
   - Total amount to be repaid (`$AMOUNT2PAY`)

8. **User History (Existing Borrowers):**
   - Upon submitting the request, the system (possibly using a background process):
     - Fetches the borrower's history from the database (assuming tables like `loans` and `borrower_history` exist).
     - Calculates statistics:
       - Number of loans paid off (`X total loans paid off, i.e. $1200 loans paid back`)
       - Number of outstanding loans (`Y amount of total loans outstanding, i.e. $50 loans outstanding`)
       - Total number of loans (`Z number of total loans. i.e. has received 4 loans`)
     - Displays the calculated user history.

   - **Potential Optimization:** Depending on data volume and frequency of requests, consider caching user history for improved performance.

**Lender Response**

9. **Lender Notification:** The system identifies potential lenders and notifies them about the loan request.
10. **Lender Decision:** The lender decides whether to accept or reject the request.

**Lender Accepts (Smart Contract & Blockchain)**

**Assumptions:**

- A smart contract on the chosen blockchain platform manages the loan process.
- The smart contract holds the initial loan amount deposited by the borrower.

11. **Smart Contract Funds:** The smart contract takes the requested amount (`$AMOUNTREQUEST`) from the borrower's deposited funds.
12. **Lender Wallet Check:** The smart contract prompts the lender to connect their wallet and verify the requested amount.
13. **Lender Approval:** Upon confirmation, the lender submits a transaction via their wallet to approve the loan.

**Blockchain Transaction & SQL Entry**

14. **Blockchain Query:** Begfi uses the blockchain's API to query the transaction and confirm if the funds (`$AMOUNTREQUEST`) have been transferred to the smart contract.
15. **SQL Entry:** Upon successful transfer confirmation:
   - Update the loan status in the database (e.g., `loans` table) to "Funded."
   - Record the loan details in the `loans` table with relevant columns like `borrower_id`, `lender_id`, `amount`, `time_to_repay`, `total_amount_to_pay`, and `status`.

**Additional Considerations**

- **Security:** Implement robust security measures for user data, transactions, and smart contract interactions.
- **Error Handling:** Implement proper error handling mechanisms to gracefully handle unexpected situations.
- **Scalability:** Consider scalability solutions (e.g., database sharding, blockchain scaling solutions) if anticipated user base or transaction volume is high.
- **Regulatory Compliance:** Ensure compliance with relevant regulations related to financial transactions and user privacy
