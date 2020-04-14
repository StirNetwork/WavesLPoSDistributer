# WavesLPoSDistributer          v2.3.4
A revenue distribution tool for Waves nodes and the leasers

Welcome to Plukkies version of the LPoSdistribution script, 'the lazy' version.
This version enhances the ease of use and creates a 'one stop touch' for the user.
It also creates extra logging and usefull screen output and has integration with
Cloud Providers.
The payout jobs are nicely queued now and don't have to be executed instantly anymore
after the collector session has been executed.

Use this version if you like stuff that is automated for you! :-)

Many thanks to original version of Marc Jansen and the fork of W0utje!

Donations are welcome if you like this version of the script: 'The lazy' version
  - you can DONATE Waves to;
    - alias '**donatewaves@plukkie**'
    - address '**3PKQKCw6DdqCvuVgKtZMhNtwzf2aTZygPu6**'
  - you can LEASE your Waves to;
    - alias '**plukkieforger**' or '**plukkieleasing**'
    - address '**3P7ajba4wWLXq6t1G8VaoaVqbUb1dDp8fm4**'

## Installation steps: prerequisits
First of all, you need to install Node.js (https://nodejs.org/en/) and npm.
This version is succesfully tested with versions;
 - node v10.12.0 (allthough lower should work probably)
 - npm 6.4.1 (allthough lower should work probably)
 - tested on Ubuntu 14.0 with kernel 4.4.0-116-generic (allthough of minor importance)
 - get the latest version from github: git clone https://github.com/plukkie/WavesLPoSDistributer.git

To install node.js and npm, do following steps;
 - add repository: curl -sL https://deb.nodesource.com/setup_10.x | sudo bash -
 - install both packages: sudo apt-get install -y nodejs

In release 2.2 a new tool 'txoptimizer.py' was added. This need python3.
It has been succesfully tested with python 3.6.8 on Ubuntu.
To install python3 on ubuntu: apt install python3.

Release 2.3 has integrations with Amazon AWS S3 buckets.\
Install the aws sdk -> npm install aws-sdk from within your WavesLPOSdistributer folder.
You also need to have an Aws account and have the API access credentials installed on your
machine where you run this WavesLPOSDistributer.
NOTE
This is only needed if you want to store your html reports on S3!\
See configfile explanation in chapter ## Installation steps: first time users\
for more info how to set it up.

You can now proceed with the actual scripts installation and configuration.
Read on with one of the following steps which apply to your setup;
 - Installation steps: first time users
 - Installation steps: users that already use a previous version of **Plukkies** script
 - Installation steps: users that already use one of the original versions of the script (other then Plukkies version)

## Installation steps: first time users
These steps are for users that do not use an older version of the LPoSdistributer package yet.
1. CD into the LPoS package directory : WavesLPoSDistributer
2. install the package independencies:
```sh
npm install
cp .env-sample .env
```
3. EDIT settings in the configuration file 'config.json'.
   The default file looks like this;
```sh
{
  "paymentconfig" : {
    "paymentnode_api" : "http://localhost:6869",
    "paymentnode_apikey" : "<<your api key here>>",      <-- mandatory value (also remove << and >> chars) 
    "querynode_api" : "http://localhost:6869",
    "nodename" : "<your node name>",
    "feedistributionpercentage" : "90",
    "mrtperblock" : "0",
    "leasewallet" : "<<your leasewallet here>>",      <-- mandatory value (also remove << and >> chars)
    "transactionattachment" : "NK2oQJzq7sjCvh7AjJcLjLT9Ax",
    "firstleaserblock" : "1370000",
    "paystartblock" : "1370000",
    "blockwindowsize" : "5000",
    "nopayoutaddresses" : [ ],
    "mail" : "<<your email address>>",                 <-- optional value (also remove << and >> chars)
    "payreports" : {                                   <-- optional
			"provider" : "",
			"destination" : "",
			"region" : "",
			"requesturlprefix" : ""
		},
    "socialmedia" : {                                  <-- optional
			"provider" : "",
			"destination" : "",
			"region" : ""
		}
  },
  "toolbaseconfig" : {
    "batchinfofile" : "batchinfo.json",
    "payqueuefile" : "payqueue.dat",
    "payoutfilesprefix" : "wavesleaserpayouts",
    "paymentsdonedir" : "paymentsDone/",
    "txbasefee" : "100000",
    "masstransferpertxfee" : "50000",
    "maxmasstransfertxs" : "100",
    "transactiontimeout" : "1000",
    "transactionapisuffix" : "/assets/transfer",
    "masstxapisuffix" : "/assets/masstransfer",
    "masstransferversion" : "1",
    "relevantassets" : [ "Waves", "Mrt" ],
    "optimizerdir" : "txoptimizer"
  }
}
```
The values marked with statement "<-- mandatory" need to be filled out,
so almost all values can be left untouched as you can see, only 2 to be changed.
Make sure you remove the << and >> characters of the value fields!

Here's a clarification of all key/value pairs;

**paymentconfig**   (This part is for payment data)
```
 - "paymentnode_api"
   This is the node on which you will execute your payment transactions (your forging node)
 
 - "paymentnode_apikey"
   The API key of your forging node. This key is also configured in the configuration file of your
   Waves node configurtion file.
   
   WARNING
   keep it safe and confidential to you. With the key you can POST transactions!
   For security reasons, remove 'rwx' worldrights from config.json : chmod o-rwx config.json
 
 - "querynode_api"
   This is the node (name or ip address) and tcp port of the API server where you run your queries to.
   If you run the LPOSdistributer scripts on your forging node itself, this will be the default (localhost).
   If you run the script on another host, then you use the external ip address of your forging node here.
 
 - "nodename"
   This is a textual name identifying your node. It's just a description used in some logging
 
 - "feedistributionpercentage"
   How many percent of the transaction fees in your forged blocks, are you willing to share with
   your leasers. You can also go above 100%, but then it will cost you more then you earn!
   A reasonable value could be 90%, so it means you keep 10% for the effort and cost of the node ownership.
 
 - "mrtperblock"
   This feature is depricated. There are no Mrt rewards anymore since feature 14 is activated!
   Waves blockchain has a reward in Mrt tokens for every block your node forged. This value determines
   how many Mrt tokens per block are you willing to share with your leasers.
   Put this on 0 if you don't want to share them with leasers. Be sure it's not more then what
   waves pays to you per block, which is ~9 Mrt if you want to pay some to your leasers.
   default: 0
 
 - "leasewallet"
   This is the address of your leasewallet, i.e. "3P7ajba2wWLXq5x1G8VaoaVqbUb1dDp1fm2".
   Your leasers need to lease to this this wallet address.
 
 - "transactionattachment"
   Put here a base58 encoded message (the default is: "thanks for leasing!")
   You can encode your message on an online encoder website.
 
 - "firstleaserblock"
   If you already use a previous version of Plukkieforger's WavesLPOSdistributer, then you can leave
   it to the default. No need to change!
   Put here the block where your first leaser started his lease to you. You can reveal that by
   opening the wavesclient, go to the suitcase icon and go to 'leasing'. There you scroll all the way
   down where you see your first 'incoming leasing'. Click on the three dots and then reveal 'TX info'.
   There you see which block the lease was registered.
   You can also leave the default block in the config.json as is, as long as your first leaser
   registered later then this block, else you would miss this leaser when the collector runs.
   If however, you have no leasers yet then you can also leave it to the default. When you run the
   collector to see if you forged some blocks and you have leasers, it automatically registers the
   start and stop blocks for every session. This 'lazy' version of the tool all takes care of it
   for you :-)
 
 - "paystartblock"
   If you already use a previous version of Plukkieforger's WavesLPOSdistributer, then you can leave
   it to the default. No need to change!
   Put here the first block from which you take into consideration payments. You can just leave it
   to the default or you can take the same block as the first leaser if you changed that.
   You would only need to change it if you already did some payments and want to skip those to avoid
   duplicate payments.
 
 - "blockwindowsize"
   How many blocks to scan for every subsequent collector session. Put if different, for every
   collector session it will scan X amount of blocks. ~Every one minute a block is written
   to the Waves blockchain. So, if you want to run a batch every two weeks, you would set the
   this value to 14x24x60 = 20160 blocks. If you want to do 12 collections per year, set it to 43800.
   
   NOTE
   If you want to change this parameter later, that's just fine. Keep in mind that after you
   changed it, the next collector session will still use the old value, because of the
   batchinfo.json file which keeps the settings for the upcoming collector. Every subsequent
   sessions will use the new blockwindowsize value.
 
 - "nopayoutaddresses"
   Put here wallet addresses that you want to exclude from payments. Default it's empty, so
   everyone get's payouts. The usecase is that maybe someone grants you a huge lease and does not
   expect you to share revenues with him. It could be because you won a contest. Below example
   shows two addresses that do not get revenue share;

   [ "3P6CwqcnK1wyW5TLzD15n79KbAsqAjQWXYZ",
     "3P6CanmcnK1wyW5TLzbny55KbAsqAjQWMNO" ]
 
 - "mail"
   Put here optionally your email address. It will be used in an HTML file which is created
   which shows the leasing stats. You can share this with your leasers and so they know how
   to contact you.
   
 - "payreports" : {
          "provider" : "<local> or <aws>",
			    "destination" : "<name of folder> or <name of an aws s3 bucket>",
			    "region" : "<aws region>",
			    "requesturlprefix" : "<wb url prefix of your report destination>"
          
   This feature will upload your html report, after masstx.js did the payments. The providers
   can be a a local folder or an s3 bucket on Amazon AWS.
   1. Option local folder:
      - provider -> "local"
      - destination -> "<path to your destination folder>" , like "/var/home/htdocs/"
        NOTE: folder should be there and writable.
      - region -> not used for local, leave blank
      - requesturlprefix -> url for your consumers, i.e. "https://yourdomain.nl/reports/"
   2. Option Cloud Provider AWS:
      - provider -> "AWS"
      - destination -> "s3 bucket name" , like "wavesleasingreports"
        NOTE: bucket should be there
      - region -> Amazon region, like "eu-north-1"
      - requesturlprefix -> url for your consumers. If leave blank (""), you see the
        complete Aws 3s path with report name on screen.
  
  - "socialmedia" : {  SEE descriptions at "payreports"
	"provider" : "",
	"destination" : "",
	"region" : ""
	}
 ```
**toolbaseconfig**  (This part is for application behaviour and core values)
```
 - "batchinfofile"
   This file keeps a record of the start and stop block and the batchId of the upcoming
   collector session. When a sessions starts, a backup is created of this filename
   with the extension .bak
 
 - "payqueuefile"
   This file keeps a record of the pending payment jobs.
 
 - "payoutfilesprefix"
   This string is prefixed for all the files that are created when a collector and a payment
   job has finished. It's combined with the batchID.
 
 - "paymentsdonedir"
   This is used as archival directory to store data files.
 
 - "txbasefee"
   This is the Waves transaction fee for one single transaction and the basefee for
   a masstransaction.
 
 - "masstransferpertxfee"
   This is the extra Waves transaction fee for one sub-transaction in a masstransaction.
 
 - "maxmasstransfertxs"
   This is the maximum number of sub-transactions that fit in one masstransaction.
 
 - "transactiontimeout"
   How much msecs to wait after a POST transaction to the blockchain.
   1000 means 1 second delay.
 
 - "transactionapisuffix"
   This is the Uri complement for a single transaction (type 4 transaction)

 - "masstxapisuffix"
   This is the Uri complement for a masstransaction (type 11 transaction)
 
 - "masstransferversion"
   Version 1, not used yet.
 
 - "relevantassets"
   Which assets you actually will payout to your leasers. If you would remove 'Mrt',
   then collector sessions and dry payment checks would pretent as if 'Mrt' is
   included. However, the actual payment tool would not execute transactions for it.
 
 - "optimizerdir"
   The folder where 'txoptimizer' runs are archived for backup and reference purpose.
```

WARNING
Keep the config.json file confidential!
Remove the attributes 'rwx' worldrights from config.json :
```sh
chmod o-rwx config.json
```

Now you are done with the configuration, you can proceed with chapter "Running the collector sessions".

## Installation steps: users that already use a previous version of **Plukkies** script
Just look into the **CHANGELOG.txt** file and replace the modified files and copy the new files
from the the new downloaded version to your current/old version.
If you use a version that does not have the config.json file yet (it was added in 2.0), then
you should also configure settings in there. Please see chapter "Installation steps: first time users, point 3"
where the configuration file and all settings are explained in detail.

## Installation steps: users that already use one of the original versions of the script (other then Plukkies version)
If you use other version of the script, like from Marc jansen or w0utje, it's easy migration;

1. Finish up your last collector and payment session 
2. Rename directory of your current version to 'WavesLPoSDistributer.old'. We call this the OLD version.
3. If correct, the NEW version directory is called 'WavesLPoSDistributer'
   CD into the OLD version dir and copy following files to the NEW version dir;

   - LastBlockLeasers.json
   - The last leaserpayout info file, which looks like following;
     1250000_3P7vmba4wWLXq6t1G8VaoaVqbUb1dDp8gj4.json

     This name represents the "stopblock_" from the last session + the wallet address of your node
4. Now CD into the NEW version directory
5. EDIT the config.json file (i.e with nano or vim)
   Look for the following key/value pairs and change as mentioned;
```sh
   "firstleaserblock" : "1370000",  <== Put here the block where the first active leaser registered
   "paystartblock" : "1370000",     <== Put here the "stopblock_" from step 3.
```
6. Adopt all other settings from your OLD script to the config.json file.
   Please see chapter "Installation steps: first time users, point 3" where all settings
   are explained in detail.

## Summary steps using the tool after installation
- run a collector session: ./start_collector
- run checker: ./start_checker
- pay: node masstx

If you want to run multiple collector sessions before you do a payment:
- run as many collector sessions you want: ./start_collector.sh
- when satisfied about all your collections, run checker: ./start_checker.sh
- run optimizer: ./txoptimizer.py
- execute your payment: node masstx

## Running the collector sessions
After a successful configuration of the tool, start with:
```sh
node appng.js OR start_collector.sh
```
NOTE
If you can't start 'start_collector', check if the script has execute 'x' on it.
If not add with: ```chmod u+x start_collector.sh```

NOTE\
The script can consume a serious amount of memory and exits with errors during it's run.
Therefore I've put 'start_collector.sh' script as starter which runs 'node appng.js' with
some memory optimized settings.
For me it works with tweaks to 65KB of stack memory and 8GB of available RAM. So use 'start_collector.sh'
if you run into problems and tweak to your available RAM.\
If it keeps on exitting, then shrink the block batchsize that are collected during one batch.\
This way multiple smaller batchsizes will be collected and consume less memory.
To decrease the initial batchsize, edit file config.json and set "blockwindowsize" smaller.
With smaller collector batches, you get multiple pending payments in the payment queue.
These multiple payments jobs can be merged by running the 'txoptimizer.py' tool.
Txoptimizer.py merges all pending payment jobs into one larger job. It a wonderfull way
to collect while you don't have much resources on your machine.

NOTE
To run the collector tool every night @1 AM, edit /etc/crontab and put in following line;
```sh
00 01 * * * root cd /home/myuser/WavesLPoSDistributer/ && ./start_collector.sh
```
After the tool ran, it finishes up by writing the actual payments to be done into the file
which is configured in the script by:
```sh
"payoutfilesprefix" : "wavesleaserpayouts" 
```
```sh
const crashconfig = {
        batchidstart: '0',		<== batchID here
        transactionstart: '0' }		<== last succesfull transaction +1
```
Then start the 'node massPayment.js'.
The values you can leave as is or you can put it back to 0 / 0 if you like.

## Why three seperate tools?
We decided to use seperate tools since this allows for additional tests of the payments before the payments are actually executed.
On the other hand, it does not provide any drawback since both scripts could also be called directly one after the other with:
```sh
node appng.js && node masstx.js or ./start_collector && node massPayment.js
```
However, it is strongly recommended to check the payments before the actual payments are done.
So what you could do for example, run from crontab;
- run the collector session every saterday evening
- run the checkPaymentFile every sunday, mail the output, so you have visibility
- run the txoptimizer, mail the output, so you have visibility
- run the massPayment job every tuesday

With this scheme, you have a nice automated schema and works as follows;
- Every wednesday & saterday, if the collector batchsize is still too large (because the mainnet blockheight is to low),
  the cronjob exits and waits till next collector day. If the blockrange fits, the payments are collected and
  the job is logged and queued
- Every Sunday, the checkPayment job checks the payqueue. If empty, it exits. If there is (are) job(s) in the queue,
  the paymentdata for all batchIDs are shown and your output was send by email. You have time to check the results
- Every tuesday, the payment is done for all batchIDs in the queue.

NOTE
It safe to schedule the collector and check jobs. However, regarding payments,
it's always possible that something disrupts the transaction process, in which payments
could fail and leasers don't receive payments. It's up to you, if you feel confident
with automated payments. If not, you can just execute the masstx/massPayment tool by hand.
MassPayment has forseen in the event that crashes or failed transactions (due to whatever reason) happen,
by which you can add the batchID and the number of the last succesfull transactionnr.+1, to the file
and then start massPayment.js again. Transactions will be executed from where the failures started.

If you use masstx.js, chances of crashing are much less, cause the tool bundles lots of sub-transactions
in one actual transaction. So, when doing full automation of the payouts too, it's unlikely that that crashes
occur. Cause if you have 100 leasers, you would only have 1 actual transaction which takes 1 second.

The nice thing is that the three tools are decoupled. So, if you run the collector three times a week
and the the checks every week and the payout just once a month or whenever you feel it's a good moment,
that's all fine. It also depends on the frequency of blockhits for your node and the blockwindows size
you configure. It's all up to you and it doesn't bite one another.

## A fully automated LPOS cycle
Below example is a fully automated LPOS cycle, which I use myself on a Linux sysem. It logs the cronjob tasks,\
with data and time added. It automatically collects, pending payment validation, merges with Txoptimizer and execute payment.
Everything is logged in home 'folder ~/log/'. These are the scheduled tasks;
- collect every night 45mins after 23.00 the forged block stats with appng.js (start_collector.sh)
- checks pending payments 5 minutes BEFORE txoptimizer kicks off (for validation in case of doubts or problems)
- merge all pending jobs first day of month @01.00 at night with txoptimizer.py
- checks pending payments 5 minutes AFTER txoptimizer kicks off
- execute payment first day of the month @13.00 in the afternoon (node masstx)
```
# m h  dom mon dow   command
# m h  dom mon dow   command
45 23 * * * cd /home/plukkie/WavesLPoSDistributer/ && ./start_collector.sh > ~/log/start_collector.sh-`date +\%d-\%m-\%Y_\%T`.log 2>&1
55 00 1 * * cd /home/plukkie/WavesLPoSDistributer/ && ./start_checker.sh > ~/log/start_checker.sh-`date +\%d-\%m-\%Y_\%T`.log 2>&1
00 01 1 * * cd /home/plukkie/WavesLPoSDistributer/ && ./txoptimizer.py > ~/log/txoptimizer.py-`date +\%d-\%m-\%Y_\%T`.log 2>&1
05 01 1 * * cd /home/plukkie/WavesLPoSDistributer/ && ./start_checker.sh > ~/log/start_checker.sh-`date +\%d-\%m-\%Y_\%T`.log 2>&1
0  13 1 * * cd /home/plukkie/WavesLPoSDistributer/ && node masstx.js > ~/log/masstx.js-`date +\%d-\%m-\%Y_\%T`.log 2>&1
```
With this setup, everything is done automatically and there's a good archive for logging.

## Airdrops
Payments for airdrops could be calculated by using the _airdrop.js_ script. Configuration works pretty much the same way as for the other scripts:
```sh
/*
 In the asset table replace your_asset_id to the asset ID which recognized by wavesplatform.
 Adjust airdrop amount in the config table.
 Make sure to run periodically with cron.
 (better to run manually for the first time)
*/
node airdrop.js

node airdrop.js dist
node airdrop.js balance
node airdrop.js pay
```
## dex.js
By running this script periodically, part of the data present in the blockchain is incorporated into the database.
```sh
/*
 Make sure to run periodically with cron.
*/
node dex.js
```

Payments for airdrops to leasers could be calculated by using the _airdrop_leasers.js_ script. Configuration works pretty much the same way as for the other scripts:
```sh
/**
 * Put your settings here:
 *     - address: the address of your node that you want to distribute from
 *     - total: amount of supply for the reference asset
 *     - amountToDistribute: amount of tokens that you want to distribute (have decimals in mind here...)
 *     - isStatic: boolean to select on which the sending amount is bases, true/false 
 *     			* true: every address receives amountToDistribute
 *     			* false: every address receives his percentage of amountToDistribute based on leased waves    
 *     - assetToDistributeId: id of the asset you want to airdrop
 *     - filename: name of the file the payments are written to
 *     - leasers: name of the file which contains the active leasers info, generated by app.js (LastBlockLeasers.json)
 *     - excludeList: a list of addresses that should not receive the airdrop, e.g., exchanges...
 */
var config = {
    address: '3PEFQiFMLm1gTVjPdfCErG8mTHRcH2ATaWa',
    amountToDistribute: 1,
    assetToDistributeId: '9gnc5UCY6RxtSi9FEJkcD57r5NBgdr45DVYtunyDLrgC', //BearWaves
    filename: 'airdrop_leasers.json',
    leasers: 'LastBlockLeasers.json',
    isStatic: true,
    excludeList: ["3P31zvGdh6ai6JK6zZ18TjYzJsa1B83YPoj"] //Bittrex
};
```
This example will generate the paymentfile airdrop_leasers.json for sending 1 BearWaves to every leaser in the LastBlockLeasers.json file.

Afterwards, the script could be started with:
```sh
node airdrop_leasers.js
```

## Disclaimer
Please always test your resulting payment scripts, e.g., with the _checkPaymentsFile.js_ script!
