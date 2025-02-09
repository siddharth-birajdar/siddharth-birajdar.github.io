---
title: Handling Multi-Level Hierarchy Aggregations in Tableau: FIXED
date: 2022-08-02
categories: ['Tableau','Aggregation Functios']
tags: ['tableau','fuctions','data-analysis','duplication']
author: Siddharth
---



# Handling Duplicate Aggregations in Tableau Using FIXED LOD Expressions

Tableau is a powerful tool for data visualization and aggregation, helping users uncover insights from complex datasets. However, sometimes the biggest challenges arise not from the tool itself, but from our understanding of how data behaves. I recently faced a unique issue in the retail industry, dealing with sales data for products structured under multiple hierarchies. This problem initially seemed unsolvable, but the solution turned out to be surprisingly simple.

## The Problem

In my dataset, products were assigned to multiple categories. This meant that when I aggregated the total sales, some products were counted multiple times because they existed in more than one category. As a result, the total sales figures were inflated, leading to misleading insights. At first, this seemed like a backend issue, perhaps an ETL pipeline problem or a database duplication error. Upon closer inspection, the issue was purely at the visualization layer in Tableau.

The complexity came from the fact that while each SKU had a unique identifier in the database, it was associated with multiple categories due to the hierarchical structure of the product taxonomy. Traditional aggregation methods in Tableau, such as `SUM`, counted every occurrence of an SKU, compounding the duplication issue. If aggregated at a category level, the SKU’s sale had to be attributed to each category it was present in. But if the category was to be removed, the grand total was inflated.

## The Solution: FIXED

After hours of debugging and brainstorming, the answer was remarkably straightforward: Tableau’s `FIXED` Level of Detail (LOD) expression.

Using the formula:

```
{ FIXED [SKU],[Date]:MIN([QUANTITY]) }
```

I was able to instruct Tableau to aggregate sales only at the SKU level, disregarding the category hierarchy. This ensured that each SKU was counted only once, eliminating duplicate counts while still maintaining the structure of my report.

The moment I applied this calculation, the numbers fell into place. Grand total sales figures were now accurate, aligning perfectly with backend reports.
---
image:
  path: assets/headers/fixed_function.png
  alt: Fixed Function
mermaid: true
---
## The Backend

To truly appreciate why this works, it’s important to understand Tableau’s computation engine.

Tableau performs calculations based on three primary levels:

1. **Row-Level Calculations** — These operate on each row of the dataset.
2. **Aggregate Calculations** — These summarize data at a specified level, like category or region.
3. **LOD Expressions** — These allow control over the granularity of calculations beyond standard aggregations.

`FIXED` works by specifying a level of granularity independent of the visualization itself. Unlike default aggregations, which change based on filters or dimensions in the view, this ensures that the aggregation always occurs at the defined level. In my case, by fixing the calculation at the SKU level, I prevented Tableau from summing up values at the category level where duplications existed.

## The Impact

This small tweak not only resolved the issue but also deepened my understanding of Tableau’s inner workings. What initially seemed like a complex data pipeline problem turned out to be a simple fix at the visualization level. More importantly, this experience reinforced that challenges in Tableau are often about knowing the right tool for the job rather than limitations of the software itself.

With `FIXED`, I was able to produce reliable reports, ensuring stakeholders received accurate insights without second-guessing the numbers.

## Conclusion

Tableau’s power lies in its flexibility, but with flexibility comes responsibility. Understanding how calculations work under the hood can make all the difference. More insights can be found in the [Tableau Documentation]([https://help.tableau.com/](https://help.tableau.com/current/pro/desktop/en-us/calculations_calculatedfields_lod_fixed.htm)).
