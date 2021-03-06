# Verifying Member Uniqueness

There are certain advantageous patterns for verifying the uniqueness of an item in a set. In the [`utxo-workshop`](https://github.com/nczhu/utxo-workshop), the `check_transaction` function ensures that there are no two of the same utxo's by collecting all of them into a [`BTreeMap`](https://doc.rust-lang.org/std/collections/struct.BTreeMap.html) and then checking for equality between the BTreeMap (which, like a set, does not add additional of the same element) and the original collection (which might include duplicates). This constitutes a check that the set of UTXOs are all unique and there are no duplicates.

This pattern can easily be extracted and applied to all situations for which membership uniqueness needs to be checked for some vector. In the context of the [`utxo-workshop`](https://github.com/nczhu/utxo-workshop), we have:

```rust, ignore
/// utox-workshop/runtime/src/utxo.rs `check_transaction`
{
    let input_set: BTreeMap<_, ()> =
        transaction.inputs.iter().map(|input| (input, ())).collect();

    ensure!(
        input_set.len() == transaction.inputs.len(),
        "each input must only be used once"
    );
}

{
    let output_set: BTreeMap<_, ()> = transaction
        .outputs
        .iter()
        .map(|output| (output, ()))
        .collect();

    ensure!(
        output_set.len() == transaction.outputs.len(),
        "each output must be defined only once"
    );
}
```

The use of separation into scopes minimizes the lifetime of the initialized variables within the given scopes. This should logically be done because there is no need to store the `BTreeMap<T>` for longer than is necessary.