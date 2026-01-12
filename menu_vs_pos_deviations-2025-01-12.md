# Menu vs Loyverse Deviations (2025-01-12)

- Compared `export_items-2025-01-12.csv` against `cdr-menu-2026-01-12.html` (Spanish menu only).
- Ignored CSV categories: `Bebidas Para Llevar`, `Tips`.
- POS variants are grouped by `Handle` (variant rows may omit `Name`/`Category`).

| Deviation | Menu Category | Menu Item | Menu Description | Menu Price(s) | POS Item | POS Category | POS Price(s) | Match | Details |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Missing in HTML menu | — | — | — | — | Alhambra Cañita | Cervezas | 2.5 | — | No menu item matched this POS product (after ignoring Bebidas Para Llevar and Tips). |
| Missing in HTML menu | — | — | — | — | Pan (Clásica) | Desayunos | 1 | — | No menu item matched this POS product (after ignoring Bebidas Para Llevar and Tips). |
| Missing in HTML menu | — | — | — | — | Pan (Masa Madre) | Desayunos | 1 | — | No menu item matched this POS product (after ignoring Bebidas Para Llevar and Tips). |
| Missing in HTML menu | — | — | — | — | Rum & Pepsi | Combinados | 7.5 | — | No menu item matched this POS product (after ignoring Bebidas Para Llevar and Tips). |
| Missing in POS export | DESAYUNOS | Llonguet | — | 1,0 | — | — | — | — | No confident match found in filtered CSV export. |
| Missing in POS export | COMBINADOS | Ron & Cola | — | 8,0 | — | — | — | — | No confident match found in filtered CSV export. |
| Price mismatch | VINOS | Bento | — | 5,0 / 30,0 | Blanco: Bento | Vinos y Cavas | 5.5 / 26 | name 0.72 | Menu price(s) missing in POS: 5 / 30 \| POS price(s) not shown on menu: 5.5 / 26 |
| Price mismatch | APERITIVOS | Bitter Kas (Sin Alcohol) | — | 5,0 | Bitter Kas (Sin Alcohol) | Aperitivos | 3.5 | name 1.00 | Menu price(s) missing in POS: 5 \| POS price(s) not shown on menu: 3.5 |
| Price mismatch | VINOS | Finca Olivardots | — | 5,0 / 40,0 | Tinto: Finca Olivardots | Vinos y Cavas | 7.5 / 37 | name 0.82 | Menu price(s) missing in POS: 5 / 40 \| POS price(s) not shown on menu: 7.5 / 37 |
| Price mismatch | VINOS | Gazur | — | 5,0 / 30,0 | Tinto: Gazur | Vinos y Cavas | 6.5 / 32 | name 0.72 | Menu price(s) missing in POS: 5 / 30 \| POS price(s) not shown on menu: 6.5 / 32 |
| Price mismatch | COMBINADOS | Gin & Tonic | — | 8,0 | Gin & Tonic | Combinados | 7.5 | name 1.00 | Menu price(s) missing in POS: 8 \| POS price(s) not shown on menu: 7.5 |
| Price mismatch | LICORES | Hierbas | — | 3,0 / 5,0 | Hierbas | Licores | 5 / 46 | bundle 1.00 | Menu price(s) missing in POS: 3 \| POS price(s) not shown on menu: 46 |
| Price mismatch | COMBINADOS | Vodka & Lemon | — | 8,0 | Vodka & Lemon | Combinados | 7.5 | name 1.00 | Menu price(s) missing in POS: 8 \| POS price(s) not shown on menu: 7.5 |
