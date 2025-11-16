# Reconciliation Pseudocode

Below is Python-flavoured pseudocode illustrating GenDine’s payout reconciliation logic.
This is conceptual only — no production code, no secrets.

```python
def reconcile_payout(stripe_payout):
    """
    Compare Stripe payout summary against GenDine's local ledger.
    """
    local = get_local_payout(stripe_payout.id)

    # Case 1 — Stripe payout exists, local record missing
    if local is None:
        record_missing_payout(stripe_payout)
        return Result(status="missing", details="No local record")

    # Case 2 — Total mismatch
    if local.amount != stripe_payout.amount:
        flag_discrepancy(local, stripe_payout)
        enqueue_investigation(
            payout_id=stripe_payout.id,
            expected=local.amount,
            actual=stripe_payout.amount,
        )
        return Result(status="mismatch", details="Amount discrepancy")

    # Case 3 — Per-venue mismatch
    if not amounts_match_by_venue(local, stripe_payout):
        flag_split_mismatch(local, stripe_payout)
        return Result(status="partial_mismatch", details="Per-venue mismatch")

    # Case 4 — Match
    update_reconciliation_status(local.id, status="matched")
    log_audit_event(
        payout_id=stripe_payout.id,
        matched_at=utc_now(),
        stripe_reference=stripe_payout.balance_transaction,
    )
    return Result(status="matched", details="Reconciled successfully")
