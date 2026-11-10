# Design Principles — Practice

1. Two pricing paths share a formula but differ in rounding rules. Should the formula be extracted? Identify which knowledge is truly shared.
2. A class has a `PaymentMethod` subclass for every combination of card, currency, and region. Suggest a composition-based alternative and describe its trade-off.
3. A service reads `order.customer.address.country.code`. Apply the Law of Demeter thoughtfully: what domain operation could clarify intent, and when would a wrapper add no value?
4. A design adds plugin hooks for requirements not mentioned in the prompt. Use YAGNI and extensibility reasoning to decide what to keep.
5. For a change you have made recently, name the concrete complexity that each proposed principle would reduce.
