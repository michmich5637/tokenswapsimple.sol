# tokenswapsimple.sol

A minimal **Uniswap V3** swap wrapper in Solidity: one contract, one function, one single-hop `exactInputSingle` swap.

> **Educational code.** This contract is a learning exercise. It has not been audited and must not be used in production as is. See [Security notes](#security-notes).

## Overview

`SimpleTokenSwap` pulls an ERC-20 from the caller, approves the Uniswap V3 `SwapRouter`, and swaps it for another token in a single pool.

| Property | Value |
| --- | --- |
| Contract | `SimpleTokenSwap` |
| Compiler | Solidity `^0.8.24` |
| Dependencies | Uniswap V3 `ISwapRouter`, OpenZeppelin `IERC20` (flattened in the file) |
| Fee tier | 0.3% (`3000`), hard-coded |
| License | MIT |

## Interface

```solidity
constructor(address _swapRouter, address _WETH)

function swap(
    address tokenIn,
    address tokenOut,
    uint256 amountIn,
    uint256 amountOutMinimum,
    address recipient
) external
```

## Usage

1. Deploy with the Uniswap V3 `SwapRouter` address and the WETH address of the target network.
2. Approve the deployed contract to spend `amountIn` of `tokenIn`.
3. Call `swap`. Compute `amountOutMinimum` off-chain from a quote and your slippage tolerance.

## Security notes

A short self-review of the contract, kept here on purpose:

- **No effective deadline.** `deadline: block.timestamp` always passes, so a pending transaction can be executed much later. The deadline should be a caller-supplied parameter.
- **Slippage is the caller's job.** Passing `amountOutMinimum = 0` exposes the swap to sandwich attacks.
- **Non-standard ERC-20s.** `require(IERC20.transferFrom(...))` and `require(approve(...))` revert with tokens that return no value, such as USDT. Use OpenZeppelin `SafeERC20` (`safeTransferFrom`, `forceApprove`).
- **Hard-coded fee tier.** Pairs without a 0.3% pool cannot be swapped; the fee should be a parameter.
- **Unused state.** `WETH` is stored but never used, and `swapRouter` and `WETH` could be `immutable`.

## License

MIT
