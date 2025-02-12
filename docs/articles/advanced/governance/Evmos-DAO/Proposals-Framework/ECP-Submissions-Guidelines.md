# **Proposal Submission for Voting**


## **Preparing the Proposal Payload**

Many proposals allow for long form text to be included, usually under the key `description`. These provide the opportunity to include[ markdown](https://www.markdownguide.org/) if formatted correctly as well as line breaks with `\n`. Beware, however, that if you are using the CLI to create a proposal, and setting `description` using a flag, the text will be[ escaped](https://en.wikipedia.org/wiki/Escape_sequences_in_C) which may have undesired effects. If you're using markdown or line breaks it's recommended to put the proposal text into a json file and include that file as part of the CLI proposal, as opposed to individual fields in flags.


### **Attaching Metadata**

**YAML Frontmatter**

Although not a technical requirement, it is highly recommended that you upload your complete proposal onto IPFS with the following metadata at the very beginning. For more information on YAML frontmatter, refer to[ this documentation.](https://assemble.io/docs/YAML-front-matter.html)

---

title: Launching the Evmos Liquidity Incentives Program

authors: Tricky, Benny Lava, LPX

proposal: ECP-PS-1

type: CommunityPoolSpend

funding_amt: 2000000

discussion: https://commonwealth.im/evmos/discussion/6977-prevote-kickstart-defi-on-evmos

polls: https://bafybeieyr4vsc3gubivurtvvuunhmkl3uqvsuv3zuag34gnh3xzgdyzcpe.ipfs.w3s.link/polls.json

govreviewed: true

created_at: 2022-09-17T19:14:49.280Z

---

At the end of the on-chain proposal, provide the IPFS hash or IPNS link to the proposal with the metadata with a simple `IPFS: &lt;hash or link>` at the end. This data will be used in the future for indexing and analysis purposes for off-chain governance.


### **Text Proposals**

`TextProposal` are used by delegators to agree to a certain strategy, plan, commitment, future upgrade, or any other statement in the form of text. Aside from having a record of the proposal outcome on the Evmos chain, a text proposal has no direct effect on Evmos.

**TextProposal Example Payload**


### **Community Pool**

For community pool spend proposals, there are five components:



1. **Title** - the distinguishing name of the proposal, typically the way the that explorers list proposals
2. **Description** - the body of the proposal that further describes what is being proposed and details surrounding the proposal
3. **Recipient** - the Evmos (bech32-based) address that will receive funding from the Community Pool
4. **Amount** - the amount of funding that the recipient will receive in atto-EVMOS (`aevmos`)
5. **Deposit** - the amount that will be contributed to the deposit (in `aevmos`) from the account submitting the proposal

**Community Pool Spend Payloads**

In this simple example (below), a network explorer will list the governance proposal as a `CommunityPoolSpendProposal`. When an observer selects the proposal, they'll see the description. Not all explorers will show the recipient and amount, so ensure that you verify that the description aligns with the what the governance proposal is programmed to enact. If the description says that a certain address will receive a certain number of EVMOS, it should also be programmed to do that, but it's possible that that's not the case (accidentally or otherwise).

The `amount` is `1000000000000000000aevmos`. This is equal to 1 EVMOS, so `recipient` address `evmos1mx9nqk5agvlsvt2yc8259nwztmxq7zjq50mxkp` will receive 1 EVMOS if this proposal is passed.

The `deposit` of `64000000000000000000aevmos` results in 64 EVMOS being used from the proposal submitter's account. There is a minimum deposit required for a proposal to enter the voting period, and anyone may contribute to this deposit within a 5-day period. If the minimum deposit isn't reached before this time, the deposit amounts will be burned. Deposit amounts will also be burned if quorum isn't met in the vote or if the proposal is vetoed.

**CommunityPoolSpend Example Payload**


### **Parameter Change**

Changes to the `gov` module are different from the other kinds of parameter changes because `gov` has subkeys,[ as discussed here](https://github.com/cosmos/cosmos-sdk/issues/5800).

For parameter-change proposals, there are seven components:



1. **Title** - the distinguishing name of the proposal, typically the way the that explorers list proposals
2. **Description** - the body of the proposal that further describes what is being proposed and details surrounding the proposal
3. **Subspace** - the Evmos module with the parameter that is being changed
4. **Key** - the parameter that will be changed
5. **Value** - the value of the parameter that will be changed by the governance mechanism
6. **Denom** - `aevmos` (atto-EVMOS) will be the type of asset used as the deposit
7. **Amount** - the amount that will be contributed to the deposit (in `aevmos`) from the account submitting the proposal

**ParamChange Proposal Example**

In the example below, a network explorer listed the governance proposal by its title: "Increase the minimum deposit for governance proposals." When a user selects the proposal, they'll see the proposal’s description. This proposal can be[ found on the Evmos network here](https://commonwealth.im/evmos/proposal/7-increase-the-minimum-deposit-for-governance-proposals).

Not all explorers will show the proposed parameter changes that are coded into the proposal, so the delegator should verify that the description aligns with what the governance proposal is programmed to enact. If the description says that a certain parameter will be increased, it should also be programmed to do that, but it's possible that that's not the case (accidentally or otherwise).

Users can query the proposal details with the evmosd command-line interface using this command:


```
evmosd --node https://tendermint.bd.evmos.org:26657 query gov proposal 7
```


{

 "title": "Increase the minimum deposit for governance proposals",

 "description": "If successful, this parameter-change governance proposal will change the minimum deposit for future proposals from 10 evmos tokens to 64.",

 "changes": [

   {

     "subspace": "gov",

     "key": "depositparams",

     "value": {"mindeposit":[{"denom":"aevmos","amount":"64000000000000000000"}],

     "max_deposit_period":"1209600000000000"}

   }

 ],

 "deposit": "20100000000000000000aevmos"

}

The deposit `denom` is `aevmos` and `amount` is `20100000000000000000`. Therefore, a deposit of 20.1 EVMOS will be included with this proposal. At the time, the EVMOS mainnet had a 10 EVMOS minimum deposit, so this proposal was put directly into the voting period (and subsequently passed). There is a minimum deposit required for a proposal to enter the voting period, and anyone may contribute to this deposit within a 5-day period. If the minimum deposit isn't reached before this time, the deposit amounts will be burned.


## **Submitting the Proposal**

For information on how to use `evmosd` binary to submit an on-chain proposal through the governance module.



1. `evmosd` is the command-line interface client that is used to send transactions and query Evmos
2. `tx gov submit-proposal param-change` indicates that the transaction is submitting a parameter-change proposal
3. `--from mykey` is the account key that pays the transaction fee and deposit amount
4. `--gas 500000` is the maximum amount of gas permitted to be used to process the transaction
    * the more content there is in the description of your proposal, the more gas your transaction will consume
    * if this number isn't high enough and there isn't enough gas to process your transaction, the transaction will fail
    * the transaction will only use the amount of gas needed to process the transaction
5. `--gas-prices` is the flat-rate per unit of gas value for a validator to process your transaction
6. `--chain-id evmos_9001-2` is Evmos Mainnet.
    * the testnet chain ID is `evmos_9000-4`. For current and past testnet information, please look at the[ testnet repository](https://github.com/evmos/testnets)
7. `--node` is using a full node to send the transaction to the Evmos Mainnet


### **Testnet Submission**

You may want to submit your proposal to the testnet chain before the mainnet for a number of reasons:



1. To see what the proposal description will look like
2. To signal that your proposal is about to go live on the mainnet
3. To share what the proposal will look like in advance with stakeholders
4. To test the functionality of the governance features

Submitting your proposal to the testnet increases the likelihood that you will discover a flaw before deploying your proposal on mainnet. A few things to keep in mind:



* you'll need testnet tokens for your proposal
* the parameters for testnet proposals are different (eg. voting period timing, deposit amount, deposit denomination)
* the deposit denomination is in `'atevmos'` instead of `'aevmos'`

evmosd tx gov submit-proposal \

 --title=&lt;title> \

 --description=&lt;description> \

 --type="Text" \

 --deposit="1000000atevmos" \

 --from=&lt;mykey> \

 --chain-id="evmos_9000-4"

 --node &lt;testnet-address>


### **Mainnet Submission**

This is the command format for using `evmosd` (the command-line interface) to submit your proposal on-chain:

evmosd tx gov submit-proposal \

 --title=&lt;title> \

 --description=&lt;description> \

 --type="Text" \

 --deposit="1000000aevmos" \

 --from=&lt;mykey> \

 --chain-id="evmos_9001-2"

 --node &lt;address>

Use the `evmos tx gov --help` flag to get more info about the governance commands


## **Deposit Period**

The deposit period lasts either 3 days or until the proposal deposit totals 192 EVMOS, whichever happens first.


### **Deposits**

Deposit amounts are at risk of being burned. Prior to a governance proposal entering the voting period (ie. for the proposal to be voted upon), there must be at least a minimum number of EVMOS deposited (192). Anyone may contribute to this deposit. Deposits of passed and failed proposals are returned to the contributors.

In the past, different people have considered contributions differently. There is some consensus that this should be a personal choice. There is also some consensus that this can be an opportunity for supporters to signal their support by adding to the deposit amount, so a proposer may choose to leave contribution room (ie. a deposit below 192 EVMOS) so that others may participate. It is important to remember that any contributed EVMOS are at risk of being burned.


### **Burned deposits**

Deposits are burned when proposals:



1. **Expire** - deposits will be burned if the deposit period ends before reaching the minimum deposit (192 EVMOS)
2. **Fail to reach quorum** - deposits will be burned for proposals that do not reach quorum ie. 33.4% of all staked EVMOS must vote
3. **Are vetoed** - deposits for proposals with 33.4% of voting power backing the `NoWithVeto` option are also burned


Document created by the Evmos DAO, https://evmos.community
