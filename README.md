To run the script, cd into <dir_path>/lace_coding_challenge_submission/ and run the following command:

python parser.py

or 

python3 parser.py

Depending on your environment. Please note that this script requires python3 to run as expected.

==============================================

Output format

==============================================

Results are output into a results.json file stored in the same directory as 
the parser.py script.

Each JSON object in the output contains the following fields:

'id' : User ID 

'num_purchases' : The total number of purchases made by the user

'value_of_purchases' : The total value of purchases (in cents) made by the user

'total_std_pts' : Total Standard Points: The total standard points earned from 
                  purchases (if enrolled in loyalty) made by the user.

'total_pts_redeemed' : Total Points Redeemed: The total points redeemed from 
                       purchase discounts made by the user.

'loyalty' : Boolean indicating whether the user is a member of the loyalty
            program.

==============================================

Design decisions

==============================================

1. I chose to first pass through all of the user entries at the 
   https://button-ie-coding-challenge.herokuapp.com/users endpoint, and create 
   a dictionary of User objects. I used a dictionary so that when I later
   traverse the transaction entries at the 
   https://button-ie-coding-challenge.herokuapp.com/transactions endpoint, I
   I can update User records with O(1) lookup time.

2. I created a User class to store relevant information for each user, and 
   handle updating the User state based on each transaction. 

3. Based on the contents of the transaction data set, I assumed that users earn
   10 points for every 100 cents that they spend.

4. Some of the transactions in the dataset at the transaction endpoint have 
   value 0. I still count these as valid transactions and update the users
   number of purchases.

5. Browsing the transaction data set, I noticed that in some cases (when the 
   value of the transaction is less than 500 cents) the point_differential can
   have a value that doesn't accurately reflect the number of standard points 
   earned, since the calculation to determine the point differential assumes 
   that users earn negative standard points on purchases where a user redeems 
   500 cents of credit for a purchase that has value less than 500 cents. In 
   this case, I update the users redeemed points total and do not update their 
   standard points total.

==============================================

Limitations

==============================================

1. The apply_purchase() function could easily be extended to handle Users 
   redeeming multiple increments of 5000 points in a single purchase. For
   example, redeeming 10000 points for 1000 cents of credit. However, I didn't
   see any examples of this in the transaction dataset at 
   https://button-ie-coding-challenge.herokuapp.com/transactions, so I chose 
   to assume that a user could apply only one 5000 point/500 cent discount per
   transaction.

2. In order to use the loads() function from the json library, I had to strip
   out some leading/trailing characters from the input at the users and
   transactions endpoints. 

   I assume that input from the users endpoint will always be of the format:
   {"users":[ <array of json objects> ]}

   And that input from the transactions endpoint will always be of the format:
   {"transactions":[ <array of json objects> ]}

   Since I'm only interested in the array of json objects, I strip out the
   leading and trailing characters by hardcoding the chars to strip by index.
   Because this is hardcoded, any change to the format of the input data will
   prevent the script from loading the data correctly.
