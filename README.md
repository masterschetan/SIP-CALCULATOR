
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>SIP Calculator</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js"></script>

    <style>
        /* Global Box Sizing for consistent layout */
        *, *::before, *::after {
            box-sizing: border-box;
        }

        body {
            font-family: 'Inter', Arial, sans-serif;
            margin: 0; /* Remove default body margin */
            padding: 20px; /* Add padding to body instead */
            background-color: #f5f5f5;
            -webkit-text-size-adjust: 100%; /* Prevent text scaling on iOS */
            font-size: 16px; /* Slightly larger base font for tablets */
        }
        .container {
            max-width: 1200px;
            margin: 0 auto;
            background: white;
            padding: 20px;
            border-radius: 10px;
            box-shadow: 0 2px 10px rgba(0,0,0,0.1);
        }
        .input-section {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            padding: 20px;
            border-radius: 10px;
            margin-bottom: 20px;
            color: white;
        }
        .input-group {
            display: flex;
            gap: 20px;
            margin-bottom: 15px;
            flex-wrap: wrap;
            justify-content: center; /* Center items on smaller screens */
        }
        .input-item {
            flex: 1;
            min-width: 250px; /* Slightly increased min-width for tablet inputs */
        }
        .input-item label {
            display: block;
            margin-bottom: 8px; /* Increased margin for better separation */
            font-weight: bold;
            font-size: 1.1em; /* Larger label font */
        }
        .input-item input {
            width: 100%;
            padding: 12px; /* Increased padding for easier tapping */
            border: none;
            border-radius: 8px; /* Slightly more rounded corners */
            font-size: 17px; /* Larger input text */
            -webkit-appearance: none; /* Remove default iOS input styling */
        }
        .calculate-btn, .pdf-btn {
            padding: 15px 35px; /* Larger buttons */
            border: none;
            border-radius: 8px;
            cursor: pointer;
            font-size: 18px; /* Larger button text */
            margin: 10px; /* Adjust margin for spacing */
            transition: background-color 0.3s ease, transform 0.2s ease; /* Add transform for touch feedback */
        }
        .calculate-btn {
            background: #28a745;
            color: white;
        }
        .calculate-btn:hover {
            background: #218838;
        }
        .calculate-btn:active { /* Add active state for touch feedback */
            transform: scale(0.98);
        }
        .pdf-btn {
            background: #dc3545;
            color: white;
        }
        .pdf-btn:hover {
            background: #c82333;
        }
        .pdf-btn:active { /* Add active state for touch feedback */
            transform: scale(0.98);
        }
        .summary {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(220px, 1fr)); /* Adjusted minmax for better fit on tablets */
            gap: 15px;
            margin-bottom: 20px;
        }
        .summary-item {
            padding: 20px; /* Increased padding */
            border-radius: 8px;
            text-align: center;
            font-weight: bold;
            box-shadow: 0 2px 5px rgba(0,0,0,0.05);
            font-size: 1.1em; /* Larger summary text */
        }
        .summary-item div:first-child {
            font-size: 0.9em; /* Smaller label within summary item */
            margin-bottom: 5px;
        }
        .summary-item div:last-child {
            font-size: 1.4em; /* Larger value in summary item */
        }

        /* Existing summary colors remain */
        .summary-item.pay { background: #87CEEB; color: #000; }
        .summary-item.total-paid { background: #90EE90; color: #000; }
        .summary-item.monthly-receive { background: #FF69B4; color: white; }
        .summary-item.final-amount { background: #FFD700; color: #000; }

        .table-container {
            overflow-x: auto; /* Essential for horizontal scrolling on smaller screens */
            -webkit-overflow-scrolling: touch; /* Smoother scrolling on iOS */
            margin-top: 20px;
        }
        table {
            width: 100%;
            border-collapse: collapse;
            font-size: 14px; /* Slightly larger table font */
            min-width: 768px; /* Ensure table is wide enough for iPad Pro in portrait if needed */
        }
        th, td {
            border: 1px solid #ddd;
            padding: 10px; /* Increased table cell padding */
            text-align: center;
        }
        th {
            background-color: #f8f9fa;
            font-weight: bold;
        }
        .outflow { color: #dc3545; font-weight: bold; }
        .inflow { color: #28a745; font-weight: bold; }
        .final-row { background-color: #e3f2fd; font-weight: bold; }
        .total-row { background-color: #fff3cd; font-weight: bold; }
        .inflow-option-group {
            display: flex;
            flex-direction: column;
            gap: 10px;
            margin-top: 15px;
            font-size: 1.1em; /* Larger font for radio options */
        }
        .inflow-option-group label {
            display: flex;
            align-items: center;
            gap: 10px; /* Increased gap */
            font-weight: normal;
            cursor: pointer;
            padding: 5px 0; /* Add some padding to labels */
        }
        .inflow-option-group input[type="radio"] {
            width: 20px; /* Make radio buttons larger */
            height: 20px; /* Make radio buttons larger */
            margin: 0;
            flex-shrink: 0; /* Prevent shrinking */
        }
        
        /* Focus styles for accessibility */
        input:focus, button:focus, label:focus-within {
            outline: 2px solid #007bff;
            outline-offset: 2px;
        }

        /* Responsive adjustments for very small screens (phones in portrait) */
        @media (max-width: 768px) {
            body {
                padding: 10px;
            }
            .container {
                padding: 15px;
            }
            .input-group {
                flex-direction: column;
                gap: 15px;
            }
            .input-item {
                min-width: unset; /* Remove min-width on very small screens */
                width: 100%;
            }
            .calculate-btn, .pdf-btn {
                width: 100%; /* Full width buttons on small screens */
                margin: 5px 0;
            }
            .summary {
                grid-template-columns: 1fr; /* Stack summary items on small screens */
            }
            table {
                font-size: 12px; /* Smaller table font for very narrow screens */
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <h1 style="text-align: center; color: #333; margin-bottom: 30px;">
            <a href="https://masterschetan.com/" target="_blank" style="text-decoration: none; color: inherit;">master<span style="color: red;">S</span>chetan.com</a>
        </h1>
        
        <div class="input-section">
            <div class="input-group">
                <div class="input-item">
                    <label for="monthlyPayment">Monthly Payment (Rs.)</label>
                    <input type="number" id="monthlyPayment" value="100000" min="100">
                </div>
                <div class="input-item">
                    <label for="years">Investment Period (Years)</label>
                    <input type="number" id="years" value="8" min="1">
                </div>
                <div class="input-item">
                    <label for="monthlyReceive">Monthly Receive (Rs.)</label>
                    <input type="number" id="monthlyReceive" value="100000" min="100">
                </div>
                <div class="input-item">
                    <label for="returnRate">Return Rate (%)</label>
                    <input type="number" id="returnRate" value="15" min="1" max="50" step="0.1">
                </div>
            </div>
            <div class="inflow-option-group">
                <label>
                    <input type="radio" name="inflowType" value="constant" checked>
                    Constant Monthly Inflow
                </label>
                <label>
                    <input type="radio" name="inflowType" value="doubling">
                    Inflow with 1-2-3-4 Pattern (based on Investment Period)
                </label>
            </div>
            <div style="text-align: center; margin-top: 20px;">
                <button class="calculate-btn" onclick="calculateSIP()">Calculate SIP</button>
                <button class="pdf-btn" onclick="downloadReport()">Download Report</button>
            </div>
        </div>

        <div class="summary" id="summary" style="display: none;">
            <div class="summary-item pay">
                <div>Monthly Payment</div>
                <div id="summaryPay">Rs. 1,000</div>
            </div>
            <div class="summary-item total-paid">
                <div>Total Amount Paid (96 Months)</div>
                <div id="summaryTotalPaid">Rs. 96,000</div>
            </div>
            <div class="summary-item monthly-receive">
                <div>Monthly Receive (97 Months onwards)</div>
                <div id="summaryMonthlyReceive">Rs. 1,000</div>
            </div>
            <div class="summary-item final-amount">
                <div>Final Amount</div>
                <div id="summaryFinalAmount">Rs. 1,00,46,254.49</div>
            </div>
        </div>

        <div class="table-container" id="tableContainer" style="display: none;">
            <table id="sipTable">
                <thead>
                    <tr>
                        <th>Year</th>
                        <th>Opening</th>
                        <th>Outflow</th>
                        <th>Total</th>
                        <th>Avg Return</th>
                        <th>Total + Return</th>
                        <th>Inflow</th>
                        <th>Closing Balance</th>
                    </tr>
                </thead>
                <tbody id="sipTableBody">
                </tbody>
            </table>
        </div>
    </div>

    <script>
        let calculationData = [];
        let overallTotalInflow = 0;
        
        function calculateSIP() {
            const monthlyPayment = parseFloat(document.getElementById('monthlyPayment').value);
            const years = parseInt(document.getElementById('years').value);
            const monthlyReceive = parseFloat(document.getElementById('monthlyReceive').value);
            const returnRate = parseFloat(document.getElementById('returnRate').value) / 100;
            const inflowType = document.querySelector('input[name="inflowType"]:checked').value;
            
            // Input validation
            if (isNaN(monthlyPayment) || monthlyPayment <= 0 ||
                isNaN(years) || years <= 0 ||
                isNaN(monthlyReceive) || monthlyReceive <= 0 ||
                isNaN(returnRate) || returnRate <= 0) {
                showMessageBox('Please enter valid positive numbers for all fields.');
                return;
            }

            calculationData = [];
            let totalPaid = 0;
            let previousBalance = 0;
            overallTotalInflow = 0; 
            
            const tbody = document.getElementById('sipTableBody');
            tbody.innerHTML = '';
            
            // Calculate for investment period (outflow years)
            for (let year = 1; year <= years; year++) {
                const yearlyOutflow = monthlyPayment * 12;
                totalPaid += yearlyOutflow;
                
                const opening = previousBalance;
                const total = opening + yearlyOutflow;
                const avgReturn = total * returnRate;
                const totalWithReturn = total + avgReturn;
                const inflow = 0;
                const closingBalance = totalWithReturn - inflow;
                
                calculationData.push({
                    year: year,
                    opening: opening,
                    outflow: yearlyOutflow,
                    total: total,
                    avgReturn: avgReturn,
                    totalWithReturn: totalWithReturn,
                    inflow: inflow,
                    closingBalance: closingBalance
                });
                
                previousBalance = closingBalance;
            }
            
            // Calculate for receiving period (inflow years) - up to 40 years total
            let receiveYear = years + 1;
            const maxYears = 40; 
            
            while (receiveYear <= maxYears && previousBalance > 0) {
                const opening = previousBalance;
                const outflow = 0;
                const total = opening + outflow;
                const avgReturn = total * returnRate;
                const totalWithReturn = total + avgReturn;
                
                let currentYearlyInflow = 0;
                if (inflowType === 'constant') {
                    currentYearlyInflow = monthlyReceive * 12;
                } else if (inflowType === 'doubling') {
                    const yearsInReceivingPhase = receiveYear - years; 
                    const periodIndex = Math.ceil(yearsInReceivingPhase / years);
                    currentYearlyInflow = (monthlyReceive * 12) * periodIndex;
                }

                const inflow = currentYearlyInflow;
                const closingBalance = Math.max(0, totalWithReturn - inflow);
                
                overallTotalInflow += inflow; 
                
                calculationData.push({
                    year: receiveYear,
                    opening: opening,
                    outflow: outflow,
                    total: total,
                    avgReturn: avgReturn,
                    totalWithReturn: totalWithReturn,
                    inflow: inflow,
                    closingBalance: closingBalance
                });
                
                previousBalance = closingBalance;
                receiveYear++;
            }
            
            // Update summary
            const finalClosingBalance = calculationData[calculationData.length - 1].closingBalance;
            const finalAmountCalculated = finalClosingBalance + overallTotalInflow; 

            const totalMonthsInvestment = years * 12;
            const totalMonthsReceiving = receiveYear - (years + 1); // Number of months in receiving phase
            
            document.getElementById('summaryPay').textContent = `Rs. ${monthlyPayment.toLocaleString('en-IN')}`;
            document.getElementById('summaryTotalPaid').innerHTML = `Rs. ${totalPaid.toLocaleString('en-IN')}<br><small>(${totalMonthsInvestment} Months Investment)</small>`;
            document.getElementById('summaryMonthlyReceive').innerHTML = `Rs. ${monthlyReceive.toLocaleString('en-IN')}${inflowType === 'doubling' ? ' (1-2-3-4 Pattern)' : ''}<br><small>(from month ${totalMonthsInvestment + 1} for ${totalMonthsReceiving} months)</small>`;
            document.getElementById('summaryFinalAmount').textContent = `Rs. ${finalAmountCalculated.toLocaleString('en-IN', {minimumFractionDigits: 2, maximumFractionDigits: 2})}`;
            
            displayTable();
            
            document.getElementById('summary').style.display = 'grid';
            document.getElementById('tableContainer').style.display = 'block';
        }
        
        function displayTable() {
            const tbody = document.getElementById('sipTableBody');
            tbody.innerHTML = '';
            
            calculationData.forEach(data => {
                const row = tbody.insertRow();
                row.innerHTML = `
                    <td>${data.year}</td>
                    <td>${data.opening.toLocaleString('en-IN', {minimumFractionDigits: 2, maximumFractionDigits: 2})}</td>
                    <td class="${data.outflow > 0 ? 'outflow' : ''}">${data.outflow.toLocaleString('en-IN')}</td>
                    <td>${data.total.toLocaleString('en-IN', {minimumFractionDigits: 2, maximumFractionDigits: 2})}</td>
                    <td>${data.avgReturn.toLocaleString('en-IN', {minimumFractionDigits: 2, maximumFractionDigits: 2})}</td>
                    <td>${data.totalWithReturn.toLocaleString('en-IN', {minimumFractionDigits: 2, maximumFractionDigits: 2})}</td>
                    <td class="${data.inflow > 0 ? 'inflow' : ''}">${data.inflow.toLocaleString('en-IN')}</td>
                    <td>${data.closingBalance.toLocaleString('en-IN', {minimumFractionDigits: 2, maximumFractionDigits: 2})}</td>
                `;
                
                if (data.year === calculationData[calculationData.length - 1].year) {
                    row.classList.add('final-row');
                }
            });
            
            // Add total row
            const totalRow = tbody.insertRow();
            const totalPaid = calculationData.filter(d => d.outflow > 0).reduce((sum, d) => sum + d.outflow, 0);
            const totalReceived = overallTotalInflow; 
            const finalClosingBalance = calculationData[calculationData.length - 1].closingBalance;
            const finalAmountForTotalRow = finalClosingBalance + totalReceived;
            
            totalRow.classList.add('total-row');
            totalRow.innerHTML = `
                <td colspan="2"><strong>TOTAL</strong></td>
                <td class="outflow"><strong>${totalPaid.toLocaleString('en-IN')}</strong></td>
                <td colspan="3"></td>
                <td class="inflow"><strong>${totalReceived.toLocaleString('en-IN')}</strong></td>
                <td><strong>${finalAmountForTotalRow.toLocaleString('en-IN', {minimumFractionDigits: 2, maximumFractionDigits: 2})}</strong></td>
            `;
        }
        
        function downloadReport() {
            if (calculationData.length === 0) {
                showMessageBox('Please calculate SIP first!');
                return;
            }

            console.log('Starting PDF generation...');

            const monthlyPayment = parseFloat(document.getElementById('monthlyPayment').value);
            const years = parseInt(document.getElementById('years').value);
            const monthlyReceive = parseFloat(document.getElementById('monthlyReceive').value);
            const returnRate = parseFloat(document.getElementById('returnRate').value);
            const inflowType = document.querySelector('input[name="inflowType"]:checked').value;
            
            const finalClosingBalance = calculationData[calculationData.length - 1].closingBalance;
            const finalAmountForPdf = finalClosingBalance + overallTotalInflow; 

            const totalMonthsInvestment = years * 12;
            const totalPaidInvestmentOnly = monthlyPayment * totalMonthsInvestment; 
            const totalMonthsReceiving = (calculationData.length * 12) - totalMonthsInvestment;
            
            if (typeof window.jspdf === 'undefined' || typeof html2canvas === 'undefined') {
                console.error('jsPDF or html2canvas not loaded');
                fallbackDownload();
                return;
            }

            try {
                const { jsPDF } = window.jspdf;
                const pdf = new jsPDF('p', 'mm', 'a4');
                let yPos = 20;

                pdf.setFontSize(20);
                pdf.text('SIP Calculator Report', 105, yPos, { align: 'center' });
                yPos += 10;

                pdf.setFontSize(12);
                pdf.text(`Generated on: ${new Date().toLocaleDateString()}`, 105, yPos, { align: 'center' });
                yPos += 20;

                pdf.setFontSize(16);
                pdf.text('Investment Summary', 20, yPos);
                yPos += 15;

                pdf.setFontSize(12);
                pdf.text(`Monthly Payment: Rs. ${monthlyPayment.toLocaleString('en-IN')}`, 20, yPos);
                yPos += 8;
                pdf.text(`Investment Period: ${years} years (${totalMonthsInvestment} months)`, 20, yPos);
                yPos += 8;
                pdf.text(`Total Amount Invested: Rs. ${totalPaidInvestmentOnly.toLocaleString('en-IN')}`, 20, yPos);
                yPos += 8;
                pdf.text(`Monthly Receive: Rs. ${monthlyReceive.toLocaleString('en-IN')} ${inflowType === 'doubling' ? ' (1-2-3-4 Pattern)' : ''} (from month ${totalMonthsInvestment + 1} for ${totalMonthsReceiving} months)`, 20, yPos);
                yPos += 8;
                pdf.text(`Return Rate: ${returnRate}%`, 20, yPos);
                yPos += 8;
                pdf.text(`Final Amount: Rs. ${finalAmountForPdf.toLocaleString('en-IN', { minimumFractionDigits: 2, maximumFractionDigits: 2 })}`, 20, yPos);

                yPos += 20;
                pdf.setFontSize(16);
                pdf.text('Key Insights:', 20, yPos);

                pdf.setFontSize(11);
                yPos += 12;
                pdf.text(`• You invest Rs. ${monthlyPayment.toLocaleString('en-IN')} monthly for ${years} years`, 20, yPos);
                yPos += 8;
                pdf.text(`• Total investment: Rs. ${totalPaidInvestmentOnly.toLocaleString('en-IN')}`, 20, yPos);
                yPos += 8;
                pdf.text(`• From year ${years + 1}, you receive Rs. ${monthlyReceive.toLocaleString('en-IN')} ${inflowType === 'doubling' ? ' (1-2-3-4 Pattern)' : ''} monthly`, 20, yPos);
                yPos += 8;
                pdf.text(`• Final amount (Closing Balance + Total Inflow Received): Rs. ${finalAmountForPdf.toLocaleString('en-IN', { minimumFractionDigits: 2, maximumFractionDigits: 2 })}`, 20, yPos);
                yPos += 8;
                pdf.text(`• This plan provides both regular income and wealth preservation`, 20, yPos);
                
                pdf.addPage();
                yPos = 20;
                pdf.setFontSize(16);
                pdf.text('Year-wise Breakdown', 20, yPos);
                yPos += 10;

                const table = document.getElementById('sipTable');

                html2canvas(table, {
                    scale: 2, 
                    logging: true,
                    useCORS: true 
                }).then(canvas => {
                    const imgData = canvas.toDataURL('image/png');
                    const imgWidth = 190;
                    const pageHeight = pdf.internal.pageSize.height;
                    const imgHeight = canvas.height * imgWidth / canvas.width;
                    let heightLeft = imgHeight;

                    let position = yPos; 

                    pdf.addImage(imgData, 'PNG', 10, position, imgWidth, imgHeight);
                    heightLeft -= (pageHeight - position);

                    while (heightLeft > 0) {
                        pdf.addPage();
                        position = - (imgHeight - heightLeft); 
                        pdf.addImage(imgData, 'PNG', 10, position, imgWidth, imgHeight);
                        heightLeft -= pageHeight;
                    }

                    pdf.save('SIP-Calculator-Report.pdf');
                    console.log('PDF generated successfully');
                }).catch(error => {
                    console.error('Error generating PDF with html2canvas:', error);
                    fallbackDownload();
                });

            } catch (error) {
                console.error('Error generating PDF:', error);
                fallbackDownload();
            }
        }
        
        function fallbackDownload() {
            const monthlyPayment = parseFloat(document.getElementById('monthlyPayment').value);
            const years = parseInt(document.getElementById('years').value);
            const monthlyReceive = parseFloat(document.getElementById('monthlyReceive').value);
            const returnRate = parseFloat(document.getElementById('returnRate').value);
            const inflowType = document.querySelector('input[name="inflowType"]:checked').value;
            
            const finalClosingBalance = calculationData[calculationData.length - 1].closingBalance;
            const finalAmountForHtml = finalClosingBalance + overallTotalInflow; 
            
            const totalMonthsInvestment = years * 12;
            const totalPaidInvestmentOnly = monthlyPayment * totalMonthsInvestment;
            const totalMonthsReceiving = (calculationData.length * 12) - totalMonthsInvestment;
            
            let htmlReport = `
<!DOCTYPE html>
<html>
<head>
    <title>SIP Calculator Report</title>
    <style>
        body { font-family: Arial, sans-serif; margin: 20px; line-height: 1.6; }
        .header { text-align: center; color: #333; margin-bottom: 30px; }
        .summary { background: #f8f9fa; padding: 20px; border-radius: 8px; margin-bottom: 20px; }
        .summary-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; margin-top: 15px; }
        .insights { background: #e8f5e8; padding: 20px; border-radius: 8px; margin-bottom: 20px; }
        table { width: 100%; border-collapse: collapse; margin-top: 20px; }
        th, td { border: 1px solid #ddd; padding: 8px; text-align: center; font-size: 12px; }
        th { background-color: #3498db; color: white; font-weight: bold; }
        .outflow { color: #e74c3c; font-weight: bold; }
        .inflow { color: #27ae60; font-weight: bold; }
        .print-btn { margin: 20px 0; padding: 10px 20px; background: #3498db; color: white; border: none; cursor: pointer; }
        @media print { .print-btn { display: none; } }
    </style>
</head>
<body>
    <div class="header">
        <h1>SIP Calculator Report</h1>
        <p>Generated on: ${new Date().toLocaleDateString()}</p>
    </div>
    
    <div class="summary">
        <h2>Investment Summary</h2>
        <div class="summary-grid">
            <div><strong>Monthly Payment:</strong> Rs. ${monthlyPayment.toLocaleString('en-IN')}</div>
            <div><strong>Investment Period:</strong> ${years} years (${totalMonthsInvestment} months)</div>
            <div><strong>Total Amount Invested:</strong> Rs. ${totalPaidInvestmentOnly.toLocaleString('en-IN')}</div>
            <div><strong>Monthly Receive:</strong> Rs. ${monthlyReceive.toLocaleString('en-IN')}</div>
            <div><strong>Return Rate:</strong> ${returnRate}%</div>
            <div><strong>Final Amount:</strong> Rs. ${finalAmountForHtml.toLocaleString('en-IN', {minimumFractionDigits: 2, maximumFractionDigits: 2})}</div>
        </div>
    </div>
    
    <div class="insights">
        <h3>Key Insights:</h3>
        <ul>
            <li>You invest Rs. ${monthlyPayment.toLocaleString('en-IN')} monthly for ${years} years</li>
            <li>Total investment: Rs. ${totalPaidInvestmentOnly.toLocaleString('en-IN')}</li>
            <li>From year ${years + 1}, you receive Rs. ${monthlyReceive.toLocaleString('en-IN')} ${inflowType === 'doubling' ? ' (1-2-3-4 Pattern)' : ''} monthly</li>
            <li>Final amount (Closing Balance + Total Inflow Received): Rs. ${finalAmountForHtml.toLocaleString('en-IN', {minimumFractionDigits: 2, maximumFractionDigits: 2})}</li>
            <li>This plan provides both regular income and wealth preservation</li>
        </ul>
    </div>
    
    <button class="print-btn" onclick="window.print()">Print Report (Ctrl+P)</button>
    
    <h2>Year-wise Breakdown</h2>
    <table>
        <thead>
            <tr>
                <th>Year</th>
                <th>Opening Balance</th>
                <th>Outflow</th>
                <th>Total</th>
                <th>Average Return</th>
                <th>Total + Return</th>
                <th>Inflow</th>
                <th>Closing Balance</th>
            </tr>
        </thead>
        <tbody>`;
            
            calculationData.forEach(data => {
                htmlReport += `
            <tr>
                <td>${data.year}</td>
                <td>Rs. ${data.opening.toLocaleString('en-IN', {maximumFractionDigits: 0})}</td>
                <td class="${data.outflow > 0 ? 'outflow' : ''}">Rs. ${data.outflow.toLocaleString('en-IN')}</td>
                <td>Rs. ${data.total.toLocaleString('en-IN', {maximumFractionDigits: 0})}</td>
                <td>Rs. ${data.avgReturn.toLocaleString('en-IN', {maximumFractionDigits: 0})}</td>
                <td>Rs. ${data.totalWithReturn.toLocaleString('en-IN', {maximumFractionDigits: 0})}</td>
                <td class="${data.inflow > 0 ? 'inflow' : ''}">Rs. ${data.inflow.toLocaleString('en-IN')}</td>
                <td>Rs. ${data.closingBalance.toLocaleString('en-IN', {maximumFractionDigits: 0})}</td>
            </tr>`;
            });
            
            htmlReport += `
        </tbody>
    </table>
</body>
</html>`;
            
            const blob = new Blob([htmlReport], { type: 'text/html' });
            const url = URL.createObjectURL(blob);
            const a = document.createElement('a');
            a.href = url;
            a.download = 'SIP-Calculator-Report.html';
            document.body.appendChild(a);
            a.click();
            document.body.removeChild(a);
            URL.revokeObjectURL(url);
            
            showMessageBox('PDF generation failed. Report downloaded as HTML! Open the file and use Ctrl+P to print or save as PDF.');
        }

        function showMessageBox(message) {
            const messageBox = document.createElement('div');
            messageBox.style.cssText = `
                position: fixed;
                top: 50%;
                left: 50%;
                transform: translate(-50%, -50%);
                background-color: #fff;
                border: 1px solid #ccc;
                box-shadow: 0 4px 8px rgba(0,0,0,0.2);
                padding: 20px;
                border-radius: 8px;
                z-index: 1000;
                text-align: center;
                font-family: Arial, sans-serif;
                max-width: 80%;
                font-size: 1.1em; /* Larger message box text */
            `;
            messageBox.innerHTML = `
                <p>${message}</p>
                <button style="
                    background-color: #007bff;
                    color: white;
                    border: none;
                    padding: 12px 25px; /* Larger button in message box */
                    border-radius: 5px;
                    cursor: pointer;
                    margin-top: 15px;
                    font-size: 1em;
                ">OK</button>
            `;
            document.body.appendChild(messageBox);

            messageBox.querySelector('button').onclick = () => {
                document.body.removeChild(messageBox);
            };
        }
        
        // Initialize with default calculation
        calculateSIP();
    </script>
</body>
</html>
