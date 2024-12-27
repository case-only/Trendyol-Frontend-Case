# Overdue Loans Table Validation

This project provides a function to validate the correctness of table row coloring in an HTML document representing overdue book loans. Rows containing overdue loans should have a red background, and this function identifies rows where the coloring is incorrect.

## Problem Description

An HTML document contains a table where each row represents a book loan with the following columns:

1. **Name of the customer**
2. **Date the book was borrowed** (ISO 8601 format)
3. **Date the book was returned** (can be empty if the book has not yet been returned)

### Rules for Overdue Loans

A loan is considered overdue if the book has been borrowed for more than `limit` days, where `limit` is provided as input:

- The book was returned more than `limit` days after it was borrowed, or
- The book has been borrowed more than `limit` days ago and has not yet been returned.

Rows containing overdue loans should have a red background color (`style="background-color: red"`).

### Task

Write a function `solution(today, limit)` to validate the table:
- `today`: Current date in ISO 8601 format.
- `limit`: The number of days after which a loan is considered overdue.

The function returns the number of rows where the coloring does not match the overdue status.

## Assumptions

1. The document contains exactly one table.
2. Every row in the table has three cells.
3. The red background is specified only by `style="background-color: red"`.
4. Dates are correctly formatted in ISO 8601.
5. Dates fall within the range `1980-01-01` to `2016-12-31`.
6. jQuery 2.1 is available.

## Example

### Input

Given the following table in the document:

```html
<table><tbody>
    <tr style="background-color: red">
        <td>Donna</td>
        <td>2016-10-01</td>
        <td></td>
    </tr>
    <tr style="background-color: red">
        <td>Nansen</td>
        <td>2016-11-05</td>
        <td>2016-11-14</td>
    </tr>
    <tr>
        <td>Billie</td>
        <td>2015-08-14</td>
        <td>2015-10-09</td>
    </tr>
</tbody></table>
```

And input parameters:
- `today = "2016-11-30"`
- `limit = 14`

### Output

The function should return `1` since one row ("Billie") is overdue but not marked red.

## Implementation

```javascript
function solution(today, limit) {
    const todayDate = new Date(today);
    let incorrectRows = 0;

    $('table tbody tr').each(function () {
        const cells = $(this).find('td');
        const borrowedDate = new Date(cells.eq(1).text());
        const returnedDateText = cells.eq(2).text();
        const returnedDate = returnedDateText ? new Date(returnedDateText) : null;
        const isRed = $(this).attr('style') === 'background-color: red';

        let overdue = false;
        if (returnedDate) {
            overdue = (returnedDate - borrowedDate) / (1000 * 60 * 60 * 24) > limit;
        } else {
            overdue = (todayDate - borrowedDate) / (1000 * 60 * 60 * 24) > limit;
        }

        if (overdue !== isRed) {
            incorrectRows++;
        }
    });

    return incorrectRows;
}
```

## How It Works

1. Parse the `today` date.
2. Iterate through each row in the table using jQuery.
3. Extract the borrowed and returned dates from the cells.
4. Determine whether the row is overdue based on the `limit`.
5. Check if the row's background color matches its overdue status.
6. Count rows where the coloring is incorrect.

## Notes

- Ensure that the table structure follows the specified format.
- Test the function thoroughly with different `today` and `limit` values.
