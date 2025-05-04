**1. Formula**
<details>
  <summary>Click to expand/collapse the Formula Breakdown</summary>

```excel
=IFERROR(
    SUM(
        IFS(
            MATCH(INDEX(tax_range,ROWS(tax_range) - COUNTIF(tax_range, ">"&D3) + 1),tax_range) = 1,
            D3 * INDEX(tax_rates,ROWS(tax_range) - COUNTIF(tax_range, ">"&D3) + 1),



            SEQUENCE(MATCH(INDEX(tax_range,ROWS(tax_range) - COUNTIF(tax_range, ">"&D3) + 1),tax_range)) = 1,
            OFFSET(tax_range,0,0,ROWS(tax_range) - COUNTIF(tax_range, ">"&D3) + 1) * OFFSET(tax_rates,0,0,ROWS(tax_range) - COUNTIF(tax_range, ">"&D3) + 1),

            SEQUENCE(MATCH(INDEX(tax_range,ROWS(tax_range) - COUNTIF(tax_range, ">"&D3) + 1),tax_range)) <= ROWS(OFFSET(tax_range,0,0,ROWS(tax_range) - COUNTIF(tax_range, ">"&D3))),
            (INDEX(OFFSET(tax_range,0,0,ROWS(tax_range) - COUNTIF(tax_range, ">"&D3) + 1),SEQUENCE(ROWS(OFFSET(tax_range,0,0,ROWS(tax_range) - COUNTIF(tax_range, ">"&D3) + 1))))
             -
             INDEX(OFFSET(tax_range,0,0,ROWS(tax_range) - COUNTIF(tax_range, ">"&D3) + 1),SEQUENCE(ROWS(OFFSET(tax_range,0,0,ROWS(tax_range) - COUNTIF(tax_range, ">"&D3) + 1))) - 1))
            * OFFSET(tax_rates,0,0,ROWS(tax_range) - COUNTIF(tax_range, ">"&D3) + 1),



            SEQUENCE(MATCH(INDEX(tax_range,ROWS(tax_range) - COUNTIF(tax_range, ">"&D3) + 1),tax_range)) > ROWS(OFFSET(tax_range,0,0,ROWS(tax_range) - COUNTIF(tax_range, ">"&D3))),
            (D3 - INDEX(tax_range,ROWS(OFFSET(tax_range,0,0,ROWS(tax_range) - COUNTIF(tax_range, ">"&D3) + 1)) - 1))
            * INDEX(tax_rates,ROWS(OFFSET(tax_range,0,0,ROWS(tax_range) - COUNTIF(tax_range, ">"&D3) + 1)))
        )
    ),
    ""
)
```

</details>

**2. Formula Breakdown:**

This Excel formula calculates the tiered tax by evaluating the income against the defined tax brackets and applying the corresponding rates. The formula is wrapped in an `IFERROR` function to handle potential errors gracefully.

**1. `IFERROR(..., "")`:**
   - This is the outermost function. If any part of the formula within the first argument results in an error (e.g., if the income in cell `D2` is not a number), the `IFERROR` function will return an empty string (`""`), preventing the display of error messages in the output cell.

**2. `SUM(IFS(...))`:**
   - The `IFS` function is used to evaluate a series of conditions in order. It returns the value associated with the first condition that evaluates to `TRUE`.
   - The `SUM` function then adds up the results from the different conditions within the `IFS` function. This is crucial because the formula calculates the tax in segments based on the different tax brackets.

**3. `MATCH(INDEX(tax_range,ROWS(tax_range)-COUNTIF(tax_range,">"&D2)+1),tax_range)=1`:**
   - This is the condition for the **first tax bracket**.
     - `COUNTIF(tax_range,">"&D2)`: Counts the number of upper bounds in the `tax_range` that are greater than the income in cell `D2`.
     - `ROWS(tax_range)-COUNTIF(tax_range,">"&D2)+1`: This determines the row number within the `tax_range` that corresponds to the first tax bracket where the income falls (or the one immediately above if the income is exactly at a boundary).
     - `INDEX(tax_range, ...)`: Retrieves the upper bound value of this identified tax bracket.
     - `MATCH(..., tax_range)=1`: This checks if the retrieved upper bound is the *first* value in the `tax_range` named range. If it is, it means the income is within the first tax bracket.
     - **If `TRUE`:** The corresponding value is `D2 * INDEX(tax_rates,ROWS(tax_range)-COUNTIF(tax_range,">"&D2)+1)`, which calculates the tax by multiplying the total income by the tax rate of the first bracket.

**4. `SEQUENCE(MATCH(INDEX(tax_range,ROWS(tax_range)-COUNTIF(tax_range,">"&D2)+1),tax_range))=1`:**
   - This condition also relates to the **first tax bracket**, especially in scenarios with multiple tiers.
     - `MATCH(INDEX(tax_range,...),tax_range)`: As explained above, this finds the position of the first relevant upper bound.
     - `SEQUENCE(...)`: Generates a sequence of numbers starting from 1 up to the position returned by `MATCH`.
     - `=1`: This checks if the current number in the sequence is 1, effectively targeting the first row (representing the first relevant tax bracket).
     - **If `TRUE`:** The corresponding value is `OFFSET(tax_range,0,0,ROWS(tax_range)-COUNTIF(tax_range,">"&D2)+1) * OFFSET(tax_rates,0,0,ROWS(tax_range)-COUNTIF(tax_range,">"&D2)+1)`. This calculates the tax for the income within the first bracket by multiplying the upper bound of the first bracket by its corresponding tax rate.

**5. `SEQUENCE(MATCH(INDEX(tax_range,ROWS(tax_range)-COUNTIF(tax_range,">"&D2)+1),tax_range)) <= ROWS(OFFSET(tax_range,0,0,ROWS(tax_range)-COUNTIF(tax_range,">"&D2)))`:**
   - This condition handles the **intermediate tax brackets**.
     - `SEQUENCE(...)`: Generates a sequence up to the first relevant bracket's position.
     - `ROWS(OFFSET(tax_range,...))`: Determines the total number of relevant tax brackets up to the one containing the income.
     - `<=`: This checks if the current number in the sequence is less than or equal to the total number of relevant brackets.
     - **If `TRUE`:** The corresponding value calculates the tax for each fully passed tax bracket:
       - `(INDEX(OFFSET(tax_range,...),SEQUENCE(...)) - INDEX(OFFSET(tax_range,...),SEQUENCE(...)-1))`: Calculates the width of the current tax bracket (upper bound minus the previous upper bound).
       - `* OFFSET(tax_rates,...)`: Multiplies this width by the corresponding tax rate for that bracket.

**6. `SEQUENCE(MATCH(INDEX(tax_range,ROWS(tax_range)-COUNTIF(tax_range,">"&D2)+1),tax_range)) > ROWS(OFFSET(tax_range,0,0,ROWS(tax_range)-COUNTIF(tax_range,">"&D2)))`:**
   - This condition handles the **final tax bracket** (the one where the income falls).
     - `SEQUENCE(...)`: Generates a sequence up to the first relevant bracket's position.
     - `ROWS(OFFSET(tax_range,...))`: Determines the total number of relevant tax brackets.
     - `>`: This checks if the current number in the sequence is greater than the total number of relevant brackets (targeting the income within the final bracket).
     - **If `TRUE`:** The corresponding value calculates the tax for the portion of income in the final bracket:
       - `(D2 - INDEX(tax_range,ROWS(OFFSET(...))-1))`: Calculates the income exceeding the upper bound of the previous tax bracket.
       - `* INDEX(tax_rates,ROWS(OFFSET(...)))`: Multiplies this excess income by the tax rate of the current (final) bracket.

**7. `SUM(...)` (again):**
   - The `SUM` function at the beginning of the `IFS` structure adds together the tax amounts calculated for each relevant portion of the income based on the conditions met in the `IFS` function.
