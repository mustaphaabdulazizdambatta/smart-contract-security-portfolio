# President Elector - Findings Report

# Table of contents
- ## [Contest Summary](#contest-summary)
- ## [Results Summary](#results-summary)

- ## Medium Risk Findings
    - ### [M-01. Lack of Validation in Recursive Selection](#M-01)
    - ### [M-02. No Upper Bound for Candidate Votes (Out of Gas Risk)](#M-02)



# <a id='contest-summary'></a>Contest Summary

### Sponsor: First Flight #24

### Dates: Sep 12th, 2024 - Sep 19th, 2024

[See more contest details here](https://codehawks.cyfrin.io/c/2024-09-president-elector)

# <a id='results-summary'></a>Results Summary

### Number of findings:
- High: 0
- Medium: 2
- Low: 0



    
# Medium Risk Findings

## <a id='M-01'></a>M-01. Lack of Validation in Recursive Selection            



## Summary

the recursive function `_selectPresidentRecursive` may loop indefinitely, eventually causing a stack overflow and transaction failure.

## Vulnerability Details

The recursive \`**src/RankedChoice**::`_selectPresidentRecursive` function assumes that at least one candidate remains at each step.If somehow the function is called with an empty `candidateList`, it would cause an infinite recursion or unexpected behavior.

## Impact

Election process would fail, and the contract could become stuck in an unusable state until the issue is manually resolved.

## Tools Used

Manual Review

## Recommendations

Add a validation check to ensure that the candidate list is never empty.



```
function _selectPresidentRecursive(
    address[] memory candidateList,
    uint256 roundNumber
) internal returns (address[] memory) {
    if (candidateList.length == 0) {
        revert RankedChoice__SomethingWentWrong();
    }

    if (candidateList.length == 1) {
        return candidateList;
    }

    // Continue the recursive logic...
}
```

## <a id='M-02'></a>M-02. No Upper Bound for Candidate Votes (Out of Gas Risk)            



## Summary

The `selectPresident` function iterates through all voters and candidates, which can lead to excessive gas usage if the candidate list grows too large. This can result in out-of-gas errors or inefficient execution.

## Vulnerability Details

The `selectPresident` function iterates through all voters and candidates to tally votes. If the number of candidates grows too large, especially after multiple rounds of voting, this can lead to excessive gas usage. This is particularly concerning if the `candidateList` is large or grows over time.

## Impact

This could prevent the election from being completed, blocking the selection of a new president and rendering the contract unusable during such elections.

## Tools Used

Manual

## Recommendations

Enforce a maximum limit on the number of candidates that can participate in any single election. This ensures that the candidate list remains manageable and avoids excessive gas usage.

```Solidity
uint256 private constant MAX_CANDIDATES = 10;

function selectPresident() external {
    if (s_candidateList.length > MAX_CANDIDATES) {
        revert RankedChoice__InvalidInput();
    }

    // so....
}

```

}





