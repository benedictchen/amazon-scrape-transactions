# Amazon(.com|.co.jp) Scraper for Exporting and Analysis
Amazon transaction scraper for credit card / bank reconciliation purposes

## Instructions
- Go to Amazon (e.g. Amazon.com)
- Go to this path: `/cpe/yourpayments/transactions/`
- Paste this in the console:


```javascript

csvContent = '';

nextButton = document.querySelector('.a-span-last input');

intervalId = setInterval(() => {
   nextButton = document.querySelector('.a-span-last input');
    
    lastDate = '';
    csvContent += Array.from(document.querySelectorAll('.a-box-inner > .a-section')).map(groupEl => {
        if (groupEl.classList.contains('apx-transaction-date-container')) {
            lastDate = groupEl.textContent
            return null;
        }
        let escapedDate = JSON.stringify(lastDate.replace(/["']/ig, ''));
        return Array.from(groupEl.querySelectorAll('.apx-transactions-line-item-component-container')).map(partsEl => {
            return escapedDate + ',' + Array.from(partsEl.querySelectorAll('.a-column')).map(partEl => `"${partEl.textContent}"`).filter(v => !/Amazon.com Payments/.test(v)).filter(v => !/Pending/.test(v))
        }).join('\r\n');
    }).filter(v => !!v).join('\r\n');
    
    console.dir('grabbing this page...', csvContent);

    if (nextButton) {
        console.warn('Navigating to next page');
        nextButton.click();
    } else {
        clearInterval(intervalId);
        console.warn("DONE");
        // create and download CSV
        link = document.createElement('a')
        link.setAttribute('href', 'data:text/plain;charset=utf-8,' + encodeURIComponent(csvContent));
        link.setAttribute('download', 'amazon-purchases.csv');
        document.body.appendChild(link);
        link.click();
        link.remove();
    }
}, 2000);
```
