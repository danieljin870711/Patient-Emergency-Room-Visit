# Patient Emergency Room Visit Report 

![PatientDashboard](https://github.com/danieljin870711/Patient-Emergency-Room-Visit/blob/main/Screenshots/PatientsDashboard.png)

I created a Patient Emergency Room Visit Report dashboard for tracking the total patient visits, average satisfaction, average wait time, % referred patients vs. walk-in patients, and their demographic. 

Please see below for the KPIs on this dashboard.  

1. **Average Wait Time**: Discover how long patients typically wait before their appointments. Uncover patterns and trends that shed light on the efficiency of our healthcare system.
2. **Patient Satisfaction**: We'll explore the average satisfaction scores given by our patients. Learn about the factors that contribute to a positive patient experience and how we can enhance it.
3. **Total Patient Visits** Monthly: Get an overview of the ebb and flow of patients through our doors each month. Understand the dynamics of healthcare demand over time.
4. **Administrative vs. Non-Administrative Appointments**: Delve into the data to distinguish between appointments that involve administrative processes and those that don't. Explore the impact on wait times and patient satisfaction.
5. **Referrals and Walk-In Patients**: Uncover the balance between patients referred to specific departments and those who walk in without prior referral. How does this impact the overall patient experience?
6. **Patient Visits by Age Group and Race**: Explore the distribution of patient visits across different age groups and races. Gain insights into the diversity of healthcare needs and preferences.

I also shared the DAX code for creating the KPIs so that you can replicate them as needed. 

**Measures**
% Administrative Schedule
```
% Admins Schedule = 
    DIVIDE(
        COUNTROWS(
            FILTER(
                'Patients Dataset',
                'Patients Dataset'[patient_admin_flag] = TRUE() // Power BI can read True or False
            )
        ),
        [Total Patients]
    )
```

Female Visit
```
% Female Visit = 
    DIVIDE(
        CALCULATE([Total Patients],
        'Patients Dataset'[patient_gender] = "F"
        ),
        [Total Patients]
    )
```

% Male Visit
```
% Male Visit = 
    DIVIDE(
        CALCULATE([Total Patients],
        'Patients Dataset'[patient_gender] = "M"
        ),
        [Total Patients]
    )
```

% No Rating
```
% No Rating = 

VAR _NoRating = CALCULATE(
                    [Total Patients],
                    'Patients Dataset'[patient_sat_score] = BLANK()
)

RETURN
DIVIDE(_NoRating, [Total Patients])
```

% None - Administrative Schedule
```
% None - Admins Schedule = 
    DIVIDE(
        COUNTROWS(
            FILTER(
                'Patients Dataset',
                'Patients Dataset'[patient_admin_flag] = FALSE() // Power BI can read True or False
            )
        ),
        [Total Patients]
    )
```

% of Referred Patients
```
% of Referred Patients = 

VAR _FilterPatients = 
    CALCULATE(
        [Total Patients],
        'Patients Dataset'[department_referral] <> "none"
    )

RETURN
DIVIDE(
    _FilterPatients, 
    [Total Patients])
```

% of Un Referred Patients
```
% of Un Referred Patients = 

VAR _FilterPatients = 
    CALCULATE(
        [Total Patients],
        'Patients Dataset'[department_referral] = "none"
    )

RETURN
DIVIDE(
    _FilterPatients, 
    [Total Patients])
```

% Unknown
```
% Unknown = 
    DIVIDE(
        CALCULATE([Total Patients],
        'Patients Dataset'[patient_gender] = "NC"
        ),
        [Total Patients]
    )
```

Average Satisfaction Score
```
Average Satisfaction Score = 
    CALCULATE(
        AVERAGE('Patients Dataset'[patient_sat_score]),
        'Patients Dataset'[patient_sat_score] <> BLANK()
    )
```

Average Wait Time
```
Average Wait Time = 

VAR AvgWaitTimeVer1 = 
CALCULATE(
    AVERAGE('Patients Dataset'[patient_waittime]),
    'Patients Dataset'[patient_waittime] <> BLANK()
)

VAR AvgWaitTimeVer2 = 
CALCULATE(
    AVERAGE('Patients Dataset'[patient_waittime]),
    'Patients Dataset'[patient_waittime] <> BLANK()
)

RETURN AvgWaitTimeVer2
```

Conditional Formating Max-Point (Month)
```
CF Max Point (Month) = 

VAR _PatientTable = 
        CALCULATETABLE(
            ADDCOLUMNS(
            SUMMARIZE('Date','Date'[Month]),
            "@TotalPatients", 
            [Total Patients]
            ),
            ALLSELECTED()
        )

VAR _MinValue = MINX(_PatientTable,[@TotalPatients])
VAR _MaxValue = MAXX(_PatientTable, [@TotalPatients])
VAR _TotalPatients = [Total Patients]

RETURN
SWITCH(
    TRUE(),
    _TotalPatients = _MinValue, 0,
    _TotalPatients = _MaxValue, 1
)
```

Conditional Formating Max-Point (Year)
```
CF Max Point (Year) = 

VAR _PatientTable = 
        CALCULATETABLE(
            ADDCOLUMNS(
            SUMMARIZE('Date','Date'[Year]),
            "@TotalPatients", 
            [Total Patients]
            ),
            ALLSELECTED()
        )

VAR _MinValue = MINX(_PatientTable,[@TotalPatients])
VAR _MaxValue = MAXX(_PatientTable, [@TotalPatients])
VAR _TotalPatients = [Total Patients]

RETURN
SWITCH(
    TRUE(),
    _TotalPatients = _MinValue, 0,
    _TotalPatients = _MaxValue, 1
)
```

HitMap Caption
```
HitMap Caption = 

VAR _SelectedMeasure = 
    SELECTEDVALUE(Parameter[Parameter Order])

RETURN
IF(_SelectedMeasure = 0,
    "The darkest GREEN on the Sales denotes LOW wait Time on the Age-Group",
    "Patients are most SATISFIED when the SCALE shows the darrkest GREEN on the Age-Group")
```

Total Patients
```
Total Patients = COUNTROWS('Patients Dataset')
```

Values Max Point (Month) 
```
Values Max Point (Month) = 

VAR _PatientTable = 
        CALCULATETABLE(
            ADDCOLUMNS(
            SUMMARIZE('Date','Date'[Month]),
            "@TotalPatients", 
            [Total Patients]
            ),
            ALLSELECTED()
        )

VAR _MinValue = MINX(_PatientTable,[@TotalPatients])
VAR _MaxValue = MAXX(_PatientTable, [@TotalPatients])
VAR _TotalPatients = [Total Patients]

RETURN
SWITCH(
    TRUE(),
    _TotalPatients = _MinValue, [Total Patients],
    _TotalPatients = _MaxValue, [Total Patients]
)
```
Values Max Point (Year)
```
Values Max Point (Year) = 

VAR _PatientTable = 
        CALCULATETABLE(
            ADDCOLUMNS(
            SUMMARIZE('Date','Date'[Year]),
            "@TotalPatients", 
            [Total Patients]
            ),
            ALLSELECTED()
        )

VAR _MinValue = MINX(_PatientTable,[@TotalPatients])
VAR _MaxValue = MAXX(_PatientTable, [@TotalPatients])
VAR _TotalPatients = [Total Patients]

RETURN
SWITCH(
    TRUE(),
    _TotalPatients = _MinValue, [Total Patients],
    _TotalPatients = _MaxValue, [Total Patients]
)
```

