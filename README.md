# clone-lin-maker-rpow

Experimental i64-scale LIN clone of [makerdao/dss](https://github.com/makerdao/dss) `1.3.0` pot **rpow** (Dai Savings Rate).

- **LIN clone:** [`src/lin_maker_rpow.lin`](src/lin_maker_rpow.lin)
- **C11 oracle:** [`test/oracles/maker_rpow_c11.c`](test/oracles/maker_rpow_c11.c)
- **Proofs (results only) live in lin-open:** https://github.com/kbelludoo/lin-open
- **Event receipt:** `docs/events/EVENT_MAKER_RPOW_CLONE_LIN.rulel` in lin-open

This repository is the standalone LIN copy. It is **not** Maker pot, **not** a vat/vow adapter, and does **not** claim Solidity uint256 / mainnet / RAY=1e27 parity (`1e27` exceeds signed i64 and is rejected). The kernel is **binary fixed-point exponentiation** (`chi' = rmul(rpow(dsr, dt, RAY), chi)`). It is **not** a Lido/Rocket/Morpho share-rate and **not** Aave `rayMul`.

Upstream is AGPL-3.0-or-later. This LIN kernel is an original uint64-scale implementation of the published rpow formulas at RAY'=1e9, not a verbatim Solidity dump. Assembly `rpow` is rejected by the Solidity transpiler (`REJ_SOLIDITY_RPOW` / `REJ_SOLIDITY_RAY` / `REJ_SOLIDITY_INLINE_ASM`).

Pinned upstream: commit `3f30552a586264b32ebdb5bac94ba67020282e53` (tag `1.3.0`), `src/pot.sol` sha256 `3b20c6285e7eeec097ef20d2065e4aa1773588b9e1f127b9afe19ca3543928bb` blob `13ddce04785a0c9dc49bb8a95c441e25f4263d10`.

Canonical goldens (uint64-scale, 128-bit product, RAY'=1e9):

- `rpow(0,0,RAY')` = **1000000000** (Maker 0^0 = base); `rpow(0,5,RAY')` = **0**
- `rpow(2e9,3,RAY')` = **8000000000**; `rpow(1.5e9,2,RAY')` = **2250000000**
- `rpow(5e9,2,RAY')` = **25000000000**; naive i64 `(5e9*5e9)/1e9` wraps to **6553255926**
- `rpow(3,2,2)` half-up = **5**; skip-round floor = **4**
- `rpow(1.05e9,2,RAY')` = **1102500000**; drip `chi'=rmul(..., 2e9)` = **2205000000**
- `rpow(1e9+1000, 1000, RAY')` = **1001000482**
- `1e18 * 1e18 / 1` fail-closes to **0**
