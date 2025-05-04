# Automated Tiered Tax Calculation in Excel

This document explains an Excel formula designed for the automatic calculation of tiered taxes using dynamic named ranges.

## Solution Overview

The solution uses a single Excel formula that references two key **named ranges**:

* `tax_range`: A named range containing a single column of the **upper bounds** for each tax bracket, sorted in ascending order.
* `tax_rates`: A named range containing a single column of the **tax rates** corresponding to each upper bound in `tax_range`. **Crucially, the rates must be in the same order as the upper bounds in `tax_range`: either within a separate named range or as a parallel column in the same data table.**

The formula dynamically determines the applicable tax for each portion of the income based on these defined tiers.
