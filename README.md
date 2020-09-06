# Aidos Kuneen Javascript Library

This is the official Javascript library for the Aidos Core. It implements both the API, as well as newly proposed functionality (such as signing, bundles, utilities and conversion).

It should be noted that the Javascript Library as it stands right now is an **beta release**. As such, there might be some unexpected results.

## Getting Started

After you've successfully installed the library, it is fairly easy to get started by simply launching a new instance of the aidos object. When instantiating the object you have the option to decide the API provider that is used to send the requests to. You can either supply this directly via the `provider` option, or individually with `host` and `port`, as can be seen in the example below:

```
// Create an aidos instance with host and port as provider
var aidos = new aidos({
    'host': 'http://localhost',
    'port': 14266
});

// Create an aidos instance directly with provider
var aidos = new aidos({
    'provider': 'http://api.aidoskuneen.com:14266' // this is just an example
});

// now you can start using all of the functions
aidos.api.getNodeInfo();
```

Overall, there are currently four subclasses that are accessible from the aidos object:
- **`api`**: Core API functionality for interacting with the Aidos core. (The available API commands can be seen in the Aidos Kuneen Reference Implementation)
- **`utils`**: Utility related functions for conversions, validation and so on  
- **`multisig`**: Functions for creating and signing multi-signature addresses and transactions. (This functionality has not been completely tested yet and will be re-worked)
- **`validate`**: Validator functions that can help with determining whether the inputs or results that you get are valid.

## How to use the Library

It should be noted that most API calls are done asynchronously. What this means is that you have to utilize callbacks in order to catch the response successfully. We will add support for sync API calls, as well as event listeners in future versions.

Here is a simple example of how to access the `getNodeInfo` function:

```
aidos.api.getNodeInfo(function(error, success) {
    if (error) {
        console.error(error);
    } else {
        console.log(success);
    }
})
```

---

## API Table of Contents		

- **[api](#aidosapi)**
    - **[Standard API](#standard-api)**
    - **[getTransactionsObjects](#gettransactionsobjects)**
    - **[findTransactionObjects](#findtransactionobjects)**
    - **[getLatestInclusion](#getlatestinclusion)**
    - **[broadcastAndStore](#broadcastandstore)**
    - **[getNewAddress](#getnewaddress)**
    - **[getInputs](#getinputs)**
    - **[prepareTransfers](#preparetransfers)**
    - **[sendTrytes](#sendtrytes)**
    - **[sendTransfer](#sendtransfer)**
    - **[replayBundle](#replaybundle)**
    - **[broadcastBundle](#broadcastbundle)**
    - **[getBundle](#getbundle)**
    - **[getTransfers](#gettransfers)**
    - **[getAccountData](#getaccountdata)**
- **[utils](#aidosutils)**
    - **[convertUnits](#convertunits)**
    - **[addChecksum](#addchecksum)**
    - **[noChecksum](#nochecksum)**
    - **[transactionObject](#transactionobject)**
    - **[transactionTrytes](#transactiontrytes)**
    - **[categorizeTransfers](#categorizetransfers)**
- **[multisig](#aidosmultisig)**
    - **[getKey](#getkey)**
    - **[getDigest](#getdigest)**
    - **[addAddressDigest](#addaddressdigest)**
    - **[finalizeAddress](#finalizeaddress)**
    - **[validateAddress](#validateaddress)**
    - **[initiateTransfer](#initiatetransfer)**
    - **[addSignature](#addsignature)**
    - **[validateSignatures](#validatesignatures)**
- **[validate](#aidosvalidate)**
    - **[isAddress](#isaddress)**
    - **[isTrytes](#istrytes)**
    - **[isValue](#isvalue)**
    - **[isDecimal](#isdecimal)**
    - **[isHash](#ishash)**
    - **[isTransfersArray](#istransfersarray)**
    - **[isArrayOfHashes](#isarrayofhashes)**
    - **[isArrayOfTrytes](#isarrayoftrytes)**    
    - **[isArrayOfAttachedTrytes](#isarrayofattachedtrytes)**
    - **[isUri](#isuri)**
    - **[isInputs](#isinputs)**
    - **[isString](#isstring)**
    - **[isInt](#isint)**
    - **[isArray](#isarray)**
    - **[isObject](#isobject)**
    - **[isUri](#isuri)**
    
---

## `aidosapi`

### `Standard API`

This Javascript library has implemented all of the core API calls that are made available by the current [Aidos Reference Implementation](https://github.com/aidoskuneen/ari).

You can simply use any of the available options from the `api` object then. For example, if you want to use the `getTips` function, you would simply do it as such:

```
aidos.api.getTips(function(error, success) {
    // do stuff here
})
```

---

### `getTransactionsObjects`

Wrapper function for `getTrytes` and the Utility function `transactionObjects`. This function basically returns the entire transaction objects for a list of transaction hashes.


#### Input
```
aidos.api.getTransactionsObjects(hashes, callback)
```

1. **`hashes`**: `Array` List of transaction hashes
2. **`callback`**: `Function` callback.

#### Return Value

1. **`Array`** - list of all the transaction objects from the corresponding hashes.

---

### `findTransactionObjects`

Wrapper function for `getTrytes` and the Utility function `transactionObjects`. This function basically returns the entire transaction objects for a list of transaction hashes.


#### Input
```
aidos.api.findTransactionObjects(hashes, callback)
```

1. **`hashes`**: `Array` List of transaction hashes
2. **`callback`**: `Function` callback.

#### Return Value

1. **`Array`** - list of all the transaction objects from the corresponding hashes.

---

### `getLatestInclusion`

Wrapper function for `getNodeInfo` and `getInclusionStates`. It simply takes the most recent solid milestone as returned by getNodeInfo, and uses it to get the inclusion states of a list of transaction hashes.


#### Input
```
aidos.api.getLatestInclusion(hashes, callback)
```

1. **`hashes`**: `Array` List of transaction hashes
2. **`callback`**: `Function` callback.

#### Return Value

1. **`Array`** - list of all the inclusion states of the transaction hashes

---

### `broadcastAndStore`

Wrapper function for `broadcastTransactions` and `storeTransactions`.

#### Input
```
aidos.api.broadcastAndStore(trytes, callback)
```

1. **`trytes`**: `Array` List of transaction trytes to be broadcast and stored. Has to be trytes that were returned from `attachToMesh`
2. **`callback`**: `Function` callback.

#### Return Value

**`Object`** - empty object.

---

### `getNewAddress`

Generates a new address from a seed and returns the address. This is either done deterministically, or by providing the index of the new address to be generated.

#### Input
```
aidos.api.getNewAddress(seed [, options], callback)
```

1. **`seed`**: `String` tryte-encoded seed. It should be noted that this seed is not transferred
2. **`options`**: `Object` which is optional:
  - **`index`**: `Int` If the index is provided, the generation of the address is not deterministic.
  - **`checksum`**: `Bool` Adds 9-tryte address checksum
  - **`total`**: `Int` Total number of addresses to generate.
  - **`returnAll`**: `Bool` If true, it returns all addresses which were deterministically generated (until findTransactions returns null)
3. **`callback`**: `Function` Optional callback.

#### Returns
**`String | Array`** - returns either a string, or an array of strings.

---

### `getInputs`

Gets all possible inputs of a seed and returns them with the total balance. This is either done deterministically (by genearating all addresses until `findTransactions` returns null for a corresponding address), or by providing a key range to use for searching through.

You can also define the minimum `threshold` that is required. This means that if you provide the `threshold` value, you can specify that the inputs should only be returned if their collective balance is above the threshold value.


#### Input
```
aidos.api.getInputs(seed, [, options], callback)
```

1. **`seed`**: `String` tryte-encoded seed. It should be noted that this seed is not transferred
2. **`options`**: `Object` which is optional:
  - **`start`**: `int` Starting key index  
  - **`end`**: `int` Ending key index
  - **`threshold`**: `int` Minimum threshold of accumulated balances from the inputs that is requested
4. **`callback`**: `Function` Optional callback.

#### Return Value

1. **`Object`** - an object with the following keys:
    - **`inputs`** `Array` - list of inputs objects consisting of `address`, `balance` and `keyIndex`
    - **`totalBalance`** `int` - aggregated balance of all inputs


---

### `prepareTransfers`

Main purpose of this function is to get an array of transfer objects as input, and then prepare the transfer by **generating the correct bundle**, as well as **choosing and signing the inputs** if necessary (if it's a value transfer). The output of this function is an array of the raw transaction data (trytes).

You can provide multiple transfer objects, which means that your prepared bundle will have multiple outputs to the same, or different recipients. As single transfer object takes the values of: `address`, `value`, `message`, `tag`. The message and tag values are required to be tryte-encoded.

For the options, you can provide a list of `inputs`, that will be used for signing the transfer's inputs. It should be noted that these inputs (an array of objects) should have the provided `keyIndex` and `address` values:
```
var inputs = [{
    'keyIndex': //VALUE,
    'address': //VALUE
}]
```

The library validates these inputs then and ensures that you have sufficient balance. The `address` parameter can be used to define the address to which a remainder balance (if that is the case), will be sent to. So if all your inputs have a combined balance of 2000, and your spending 1800 of them, 200 of your coins will be sent to that remainder address. If you do not supply the `address`, the library will simply generate a new one from your seed.

#### Input
```
aidos.api.prepareTransfers(seed, transfersArray [, options], callback)
```

1. **`seed`**: `String` tryte-encoded seed. It should be noted that this seed is not transferred
2. **`transfersArray`**: `Array` of transfer objects:
  - **`address`**: `String` 81-tryte encoded address of recipient
  - **`value`**: `Int` value to be transferred.
  - **`message`**: `String` tryte-encoded message to be included in the bundle.
  - **`tag`**: `String` Tryte-encoded tag. Maximum value is 27 trytes.
3. **`options`**: `Object` which is optional:
  - **`inputs`**: `Array` List of inputs used for funding the transfer
  - **`address`**: `String` if defined, this address will be used for sending the remainder value (of the inputs) to.
4. **`callback`**: `Function` Optional callback.

#### Return Value

`Array` - an array that contains the trytes of the new bundle.

---

### `sendTrytes`

Wrapper function that does `attachToMesh` and finally, it broadcasts and stores the transactions.

#### Input
```
aidos.api.sendTrytes(trytes, depth, minWeightMagnitude, callback)
```

1. **`trytes`** `Array` trytes
2. **`depth`** `Int` depth value that determines how far to go for tip selection
3. **`minWeightMagnitude`** `Int` minWeightMagnitude
4. **`callback`**: `Function` Optional callback.

#### Returns
`Array` - returns an array of the transfer (transaction objects).

---

### `sendTransfer`

Wrapper function that basically does `prepareTransfers`, as well as `attachToMesh` and finally, it broadcasts and stores the transactions locally.

#### Input
```
aidos.api.sendTransfer(seed, depth, minWeightMagnitude, transfers [, options], callback)
```

1. **`seed`** `String` tryte-encoded seed. If provided, will be used for signing and picking inputs.
2. **`depth`** `Int` depth
3. **`minWeightMagnitude`** `Int` minWeightMagnitude
4. **`transfers`**: `Array` of transfer objects:
  - **`address`**: `String` 81-tryte encoded address of recipient
  - **`value`**: `Int` value to be transferred.
  - **`message`**: `String` tryte-encoded message to be included in the bundle.
  - **`tag`**: `String` 27-tryte encoded tag.
5. **`options`**: `Object` which is optional:
  - **`inputs`**: `Array` List of inputs used for funding the transfer
  - **`address`**: `String` if defined, this address will be used for sending the remainder value (of the inputs) to.
6. **`callback`**: `Function` Optional callback.


#### Returns
`Array` - returns an array of the transfer (transaction objects).

---

### `replayBundle`

Takes a tail transaction hash as input, gets the bundle associated with the transaction and then replays the bundle by attaching it to the Mesh.

#### Input
```
aidos.api.replayBundle(transaction [, callback])
```

1. **`transaction`**: `String` Transaction hash, has to be tail.
2. **`depth`** `Int` depth
3. **`minWeightMagnitude`** `Int` minWeightMagnitude
2. **`callback`**: `Function` Optional callback

---

### `broadcastBundle`

Takes a tail transaction hash as input, gets the bundle associated with the transaction and then rebroadcasts the entire bundle.

#### Input
```
aidos.api.broadcastBundle(transaction [, callback])
```

1. **`transaction`**: `String` Transaction hash, has to be tail.
2. **`callback`**: `Function` Optional callback

---

### `getBundle`

This function returns the bundle which is associated with a transaction. Input has to be a tail transaction (i.e. currentIndex = 0). If there are conflicting bundles (because of a replay for example) it will return multiple bundles. It also does important validation checking (signatures, sum, order) to ensure that the correct bundle is returned.

#### Input
```
aidos.api.getBundle(transaction, callback)
```

1. **`transaction`**: `String` Transaction hash of a tail transaction.
2. **`callback`**: `Function` Optional callback

#### Returns
`Array` - returns an array of the corresponding bundle of a tail transaction. The bundle itself consists of individual transaction objects.

---


### `getTransfers`

Returns the transfers which are associated with a seed. The transfers are determined by either calculating deterministically which addresses were already used, or by providing a list of indexes to get the addresses and the associated transfers from. The transfers are sorted by their timestamp. It should be noted that, because timestamps are not enforced in aidos, that this may lead to incorrectly sorted bundles (meaning that their chronological ordering in the Mesh is different).

If you want to have your transfers split into received / sent, you can use the utility function `categorizeTransfers`

#### Input
```
getTransfers(seed [, options], callback)
```

1. **`seed`**: `String` tryte-encoded seed. It should be noted that this seed is not transferred
2. **`options`**: `Object` which is optional:
  - **`start`**: `Int` Starting key index for search
  - **`end`**: `Int` Ending key index for search
  - **`inclusionStates`**: `Bool` If True, it gets the inclusion states of the transfers.
3. **`callback`**: `Function` Optional callback.

#### Returns
`Array` - returns an array of transfers. Each array is a bundle for the entire transfer.

---

### `getAccountData`

Similar to `getTransfers`, just a bit more comprehensive in the sense that it also returns the `balance` and `addresses` that are associated with your account (seed). This function is useful in getting all the relevant information of your account. If you want to have your transfers split into received / sent, you can use the utility function `categorizeTransfers`

#### Input
```
getAccountData(seed [, options], callback)
```

1. **`seed`**: `String` tryte-encoded seed. It should be noted that this seed is not transferred
2. **`options`**: `Object` which is optional:
  - **`start`**: `Int` Starting key index for search
  - **`end`**: `Int` Ending key index for search
3. **`callback`**: `Function` Optional callback.

#### Returns
`Object` - returns an object of your account data in the following format:
```
{
    'addresses': [],
    'transfers': [],
    'balance': 0
}
```

---

## `aidosutils`

All utils function are done synchronously.

---

### `convertUnits`

aidos utilizes the Standard system of Units. See below for all available units:

```
'u ADK'		: 0.000001,
'm ADK'		: 0.001,
'ADK'			: 1,
'M ADK'		: 1000000
```

#### Input
```
aidos.utils.convertUnits(value, fromUnit, toUnit)
```

1. **`value`**: `Integer || String` Value to be converted. Can be string, an integer or float.
2. **`fromUnit`**: `String` Current unit of the value. See above for the available units to utilize for conversion.
2. **`toUnit`**: `String` Unit to convert the from value into.

#### Returns
`Integer` - returns the converted unit (fromUnit => toUnit).

---

### `addChecksum`

Takes an 81-trytes address or a list of addresses as input and calculates the 9-trytes checksum of the address(es).

#### Input
```
aidos.utils.addChecksum(address)
```

1. **`address`**: `String | List` Either an individual address, or a list of addresses.

#### Returns
`String | List` - returns the 90-trytes addresses (81-trytes address + 9-trytes checksum) either as a string or list, depending on the input.

---

### `noChecksum`

Takes an 90-trytes address as input and simply removes the checksum.

#### Input
```
aidos.utils.noChecksum(address)
```

1. **`address`**: `String | List` 90-trytes address. Either string or a list

#### Returns
`String | List` - returns the 81-tryte address(es)

---

### `isValidChecksum`

Takes an 90-trytes checksummed address and returns a true / false if it is valid.

#### Input
```
aidos.utils.isValidChecksum(addressWithChecksum)
```

1. **`addressWithChecksum`**: `String` 90-trytes address  

#### Returns
`Bool` - True / False whether the checksum is valid or not

---

### `transactionObject`

Converts the trytes of a transaction into its transaction object.

#### Input
```
aidos.utils.transactionObject(trytes)
```

1. **`trytes`**: `String` 2673-trytes of a transaction  

#### Returns
`Object` - Transaction object

---

### `transactionTrytes`

Converts a valid transaction object into trytes. Please refer to [TODO] for more information what a valid transaction object looks like.

#### Input
```
aidos.utils.transactionTrytes(transactionObject)
```

1. **`transactionObject`**: `Object` valid transaction object  

#### Returns
`trytes` - converted trytes of

---

### `categorizeTransfers`

Categorizes a list of transfers into `sent` and `received`. It is important to note that zero value transfers (which for example, is being used for storing addresses in the Mesh), are seen as `received` in this function.

#### Input
```
aidos.utils.categorizeTransfers(transfers, addresses)
```

1. **`transfers`**: `List` A list of bundles. Basically is an array, of arrays (bundles), as is returned from getTransfers or getAccountData
2. **`addresses`**: 'List' List of addresses that belong to you. With these addresses as input, it's determined whether it's a sent or a receive transaction. Therefore make sure that these addresses actually belong to you.

#### Returns
`object` - the transfers categorized into `sent` and `received`


---

## `aidosmultisig`

Multi signature related functions.

> **VERY IMPORTANT NOTICE**

> This functionality has not been completely tested yet and will be re-worked, as always use is at your own risk. It is not recommended to use multisig until a full guide is released / until it is fully implemented into the wallet.

---

### `getKey`

Generates the corresponding private key of a seed.

#### Input
```
aidos.multisig.getKey(seed, index)
```

1. **`seed`**: `String` Tryte encoded seed
2. **`index`**: 'Int' Index of the private key.

#### Returns
`String` - private key represented in trytes.


---

### `getDigest`

Generates the digest value of a key.

#### Input
```
aidos.multisig.getDigest(seed, index)
```

1. **`seed`**: `String` Tryte encoded seed
2. **`index`**: 'Int' Index of the private key.

#### Returns
`String` - digest represented in trytes.

---

### `addAddressDigest`

This function is used to initiate the creation of a new multisig address. The way that it works is that the first participant of the multi-signature initiates this function with an empty curl state, and then shares the newly generated state with the other participants of the multisig address, who then basically add their key digest. Then finally, once the last co-signer added their digest, `finalizeAddress` can be used to get the actual 81-tryte address value. `validateAddress` can be used to actually validate the multi-signature.

#### Input
```
aidos.multisig.addAddressDigest(digestTrytes, curlStateTrytes)
```

1. **`digestTrytes`**: `String` digest trytes as returned by `getDigest`
2. **`curlStateTrytes`**: 'String' curl state trytes to continue modifying (which are returned by this function)

#### Returns
`String` - curl state trytes


---

### `finalizeAddress`

Finalizes the multisig address generation process and returns the correct 81-tryte address.

#### Input
```
aidos.multisig.finalizeAddress(curlStateTrytes)
```

1. **`curlStateTrytes`**: 'String' curl state trytes to continue modifying (which are returned by this function)

#### Returns
`String` - curl state trytes


---

### `validateAddress`

Validates a generated multi-sig address by getting the corresponding key digests of each of the co-signers. The order of the digests is of essence in getting correct results.

#### Input
```
aidos.multisig.validateAddress(multisigAddress, digests)
```

1. **`multisigAddress`**: `String` digest trytes as returned by `getDigest`
2. **`digests`**: 'Array' array of the key digest for each of the cosigners. The digests need to be provided in the correct signing order.

#### Returns
`Bool` - true / false

---

### `initiateTransfer`

Initiates the creation of a new transfer by generating an empty bundle with the correct number of bundle entries to be later used for the signing process. It should be noted that currently, only a single input (via `inputAddress`) is possible. The `remainderAddress` also has to be provided and should be generated by the co-signers of the multi-signature before initiating the transfer.

#### Input
```
aidos.multisig.initiateTransfer(inputAddress, remainderAddress, numCosigners, transfers, callback)
```

1. **`inputAddress`**: `String` input address which has sufficient balance and is controlled by the co-signers
2. **`remainderAddress`**: 'String' in case there is a remainder balance, send the funds to this address.
3. **`numCosigners`**: `Int` the number of co-signers for the multi-sig
4. **`transfers`**: `Array` Transfers object
5. **`callback`**: `Function`

#### Returns
`Array` - bundle

---

### `addSignature`

This function is called by each of the co-signers individually to add their signature to the bundle. Here too, order is important. This function returns the bundle, which should be shared with each of the participants of the multi-signature.

#### Input
```
aidos.multisig.addSignature(bundleToSign, cosignerIndex, inputAddress, key, callback)
```

1. **`bundleToSign`**: `Array` bundle to sign
2. **`cosignerIndex`**: `Int` total order index of the current signer in the multi-signature. Index starts at 0. e.g. If there are 4 co-signers, and you are the 3rd in order to add your signature, then your index is `2`.
3. **`inputAddress`**: 'String' input address as provided to `initiateTransfer`.
4. **`key`**: `String` private key trytes as returned by `getKey`
5. **`callback`**: `Function`

#### Returns
`Array` - bundle

---

### `validateSignatures`

This function makes it possible for each of the co-signers in the multi-signature to independently verify that a generated transaction with the corresponding signatures of the co-signers is valid. This function is safe to use and does not require any sharing of digests or key values.

#### Input
```
aidos.multisig.validateSignatures(signedBundle, inputAddress, numCosigners)
```

1. **`signedBundle`**: `Array` signed bundle by all of the co-signers
2. **`inputAddress`**: 'String' input address as provided to `initiateTransfer`.
4. **`numCosigners`**: `Int` total number of co-signers

#### Returns
`bool` - true / false  


---

## `aidosvalidate`

Validator functions. Return either true / false.

---

### `isAddress`

Checks if the provided input is a valid 81-tryte (non-checksum), or 90-tryte (with checksum) address.

#### Input
```
aidos.validate.isAddress(address)
```

1. **`address`**: `String` A single address

---

### `isTrytes`

Determines if the provided input is valid trytes. Valid trytes are: `ABCDEFGHIJKLMNOPQRSTUVWXYZ9`. If you specify the length parameter, you can also validate the input length.

#### Input
```
aidos.validate.isTrytes(trytes [, length])
```

1. **`trytes`**: `String`
2. **`length`**: `int || string` optional

---

### `isValue`

Validates the value input, checks if it's integer.

#### Input
```
aidos.validate.isValue(value)
```

1. **`value`**: `Integer`

---

### `isDecimal`

Checks if it's a decimal value

#### Input
```
aidos.validate.isDecimal(value)
```

1. **`value`**: `Integer || String`

---

### `isHash`

Checks if correct hash consisting of 81-trytes.

#### Input
```
aidos.validate.isHash(hash)
```

1. **`hash`**: `String`

---

### `isTransfersArray`

Checks if it's a correct array of transfer objects. A transfer object consists of the following values:
```
{
    'address': // STRING (trytes encoded, 81 or 90 trytes)
    'value': // INT
    'message': // STRING (trytes encoded)
    'tag': // STRING (trytes encoded, maximum 27 trytes)
}
```

#### Input
```
aidos.validate.isTransfersArray(transfersArray)
```

1. **`transfersArray`**: `array`

---

### `isArrayOfHashes`

Array of valid 81 or 90-trytes hashes.

#### Input
```
aidos.validate.isArrayOfHashes(hashesArray)
```

1. **`hashesArray`**: `Array`

---

### `isArrayOfTrytes`

Checks if it's an array of correct 2673-trytes. These are trytes either returned by prepareTransfers, attachToMesh or similar call. A single transaction object is encoded 2673 trytes.

#### Input
```
aidos.validate.isArrayOfTrytes(trytesArray)
```

1. **`trytesArray`**: `Array`

---

### `isArrayOfAttachedTrytes`

Similar to `isArrayOfTrytes`, just that in addition this function also validates that the last 243 trytes are non-zero (meaning that they don't equal 9). The last 243 trytes consist of:  `trunkTransaction` + `branchTransaction` + `nonce`. As such, this function determines whether the provided trytes have been attached to the Mesh successfully. For example this validator can be used for trytes returned by `attachToMesh`.

#### Input
```
aidos.validate.isArrayOfAttachedTrytes(trytesArray)
```

1. **`trytesArray`**: `Array`

---

### `isUri`

Work in progress.

#### Input
```
aidos.validate.isUri(uris)
```

1. **`uris`**: `Array`

---

### `isInputs`

Validates if it's an array of correct input objects. These inputs are provided to either `prepareTransfers` or `sendTransfer`. An input objects consists of the following:

```
{
    'keyIndex': // INT
    'address': // STRING
}
```

#### Input
```
aidos.validate.isInputs(inputsArray)
```

1. **`inputsArray`**: `Array`

---

### `isString`

Self explanatory.

#### Input
```
aidos.validate.isString(string)
```

---

### `isInt`

Self explanatory.

#### Input
```
aidos.validate.isInt(int)
```

---

### `isArray`

Self explanatory.

#### Input
```
aidos.validate.isArray(array)
```

---

### `isObject`

Self explanatory.

#### Input
```
aidos.validate.isObject(array)
```

---

### `isUri`

Validates a given string to check if it's a valid IPv6, IPv4 or hostname format. The string must have a `tcp://` prefix, and it may or may not have a port. Here are some example inputs:

```
tcp://[2001:db8:a0b:12f0::1]:14265
tcp://[2001:db8:a0b:12f0::1]
tcp://8.8.8.8:14265
tcp://domain.com
tcp://domain2.com:14265
```

#### Input
```
aidos.utils.isUri(node)
```

1. **`node`**: `String` IPv6, IPv4 or Hostname with or without a port.

#### Returns
`bool` - true/false if valid node format.

---
