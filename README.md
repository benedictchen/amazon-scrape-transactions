# Amazon(.com|.co.jp) Scraper for Exporting and Analysis
Amazon transaction scraper for credit card / bank reconciliation purposes

---------
Software development takes a LOT of time and it's never ending. 

If you appreciate this work, please donate and help out any way you can to keep things like this going.

If this was helpful to you, please buy me a beer on PayPal: [click here](https://www.paypal.com/cgi-bin/webscr?cmd=_s-xclick&hosted_button_id=WXQKYYKPHWXHS)
---------

## Instructions
- Go to Amazon (e.g. Amazon.com)
- Go to this path: `/cpe/yourpayments/transactions/`
- For Google Chrome: 
  - Right click and choose "Inspect"
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

---------
If this was helpful to you, please buy me a beer on PayPal: [click here](https://www.paypal.com/cgi-bin/webscr?cmd=_s-xclick&hosted_button_id=WXQKYYKPHWXHS)
---------
