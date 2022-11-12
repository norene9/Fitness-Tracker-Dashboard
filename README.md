# Fitness-Tracker-Dashboard
Avg Heart Range = [Avg Heart Rate - Target Max] - [Avg Heart Rate - Target Min]
Avg Heart Rate = AVERAGE(fctFitnessStats[Heart Rate])
Avg Heart Rate - Axis Fill = 150 - [Avg Heart Rate - Target Max]
Avg Heart Rate - Target Max = SELECTEDVALUE(dimUser[Heart Rate Target Max])
Avg Heart Rate - Target Min = SELECTEDVALUE(dimUser[Heart Rate Target Min])
Avg Heart Rate over Selected Period = 
VAR MaxDate = 
IF(
    ISFILTERED(dimDate),
    MAX(dimDate[Date]),
    [Today]
)
VAR Result =
    CALCULATE(
        AVERAGE(fctFitnessStats[Heart Rate]),
        DATESBETWEEN(dimDate[Date], MaxDate - [Calculation Period] -1, MaxDate)
    )
RETURN
Result
Avg Daily Steps = AVERAGE(fctFitnessStats[Steps])
Avg Daily Steps - Target Max = SELECTEDVALUE(dimUser[Steps Target Max])
Avg Daily Steps - Target Min = SELECTEDVALUE(dimUser[Steps Target Min])
Avg Daily Steps over Selected Period = 
VAR MaxDate = 
IF(
    ISFILTERED(dimDate),
    MAX(dimDate[Date]),
    [Today]
)
VAR Result =
    CALCULATE(
        AVERAGE(fctFitnessStats[Steps]),
        DATESBETWEEN(dimDate[Date], MaxDate - [Calculation Period] -1, MaxDate)
    )
RETURN
Result
Avg Daily Steps Range = [Avg Daily Steps - Target Max] - [Avg Daily Steps - Target Min]
Avg Calories = AVERAGE(fctFitnessStats[Calories])
Avg Calories Target Range = [Avg Daily Calories - Target Max] - [Avg Daily Calories - Target Min] 
Avg Daily Calories - Target Max = SELECTEDVALUE(dimUser[Calories Target Max])
Avg Daily Calories - Target Min = SELECTEDVALUE(dimUser[Calories Target Min])
Avg Daily Calories over Selected Period = 
VAR MaxDate = 
IF(
    ISFILTERED(dimDate),
    MAX(dimDate[Date]),
    [Today]
)
VAR Result =
    CALCULATE(
        AVERAGE(fctFitnessStats[Calories]),
        REMOVEFILTERS(dimDate),
        DATESBETWEEN(dimDate[Date], MaxDate - [Calculation Period] -1, MaxDate)
    )
RETURN
Result
Total Calories = SUM(fctFitnessStats[Calories])
Total Exercise Sessions - Target Max = SELECTEDVALUE(dimUser[Exercise Sessions (monthly) Target Max])
Total Exercise Sessions - Target Min = SELECTEDVALUE(dimUser[Exercise Sessions (monthly) Target Min])
Total Exercise Sessions over Selected Period = 
VAR MaxDate = 
IF(
    ISFILTERED(dimDate),
    MAX(dimDate[Date]),
    [Today]
)
VAR Result =
    CALCULATE(
        SUM(fctFitnessStats[Exercise Session]),
        DATESBETWEEN(dimDate[Date], MaxDate - [Calculation Period] -1, MaxDate)
    )
RETURN
Result
Total Exercise Sessions Range = [Total Exercise Sessions - Target Max] - [Total Exercise Sessions - Target Min]
Health Score = 

// Calories
VAR CaloriesTarget = ([Avg Daily Calories - Target Max] + [Avg Daily Calories - Target Min] ) / 2
VAR CaloriesActual = [Avg Daily Calories over Selected Period]
VAR CaloriesDelta = ABS(CaloriesActual - CaloriesTarget)
VAR CaloriesDeltaPct = DIVIDE( CaloriesDelta , CaloriesTarget)

// Heart Rate
VAR HeartRateTarget = ([Avg Heart Rate - Target Max] + [Avg Heart Rate - Target Min] ) / 2
VAR HeartRateActual = [Avg Heart Rate over Selected Period]
VAR HeartRateDelta = ABS(HeartRateActual - HeartRateTarget)
VAR HeartRateDeltaPct = DIVIDE( HeartRateDelta , HeartRateTarget)

// Steps
VAR StepsTarget = ([Avg Daily Steps - Target Max] + [Avg Daily Steps - Target Min] ) / 2
VAR StepsActual = [Avg Daily Steps over Selected Period]
VAR StepsDelta = ABS(StepsActual - StepsTarget)
VAR StepsDeltaPct = DIVIDE( StepsDelta , StepsTarget)

// Exercise Sessions
VAR ExerciseSessionsTarget = ([Total Exercise Sessions - Target Max]+ [Total Exercise Sessions - Target Min] ) / 2
VAR ExerciseSessionsActual = [Total Exercise Sessions over Selected Period]
VAR ExerciseSessionsDelta = ABS(ExerciseSessionsActual - ExerciseSessionsTarget)
VAR ExerciseSessionsDeltaPct = DIVIDE( ExerciseSessionsDelta , ExerciseSessionsTarget)

//Result
VAR AvgDeltaPct = ( CaloriesDeltaPct + HeartRateDeltaPct + StepsDeltaPct + ExerciseSessionsDeltaPct ) / 4
VAR ConditionShow = MAX(dimDate[Date]) <= [Today] || NOT ISFILTERED(dimDate)
VAR Result = IF( ConditionShow , 1 - ( AvgDeltaPct / 2 ) )
RETURN
    Result
    Health Score - Fill to 100 = 1 - [Health Score]
    Calculation Period = 30 //Number of days over which the KPIs are calculated
    Today = DATE(2022, 06, 28)
    Total Exercise Sessions = SUM(fctFitnessStats[Exercise Session])
    Welcome Text = 
VAR Hour = HOUR(NOW())
VAR Greeting = 
SWITCH(
    TRUE(),
    Hour >= 0 && Hour < 5, "Good Night",
    Hour >= 5 && Hour < 12, "Good Morning",
    Hour >= 12 && Hour < 18, "Good Afternoon",
    Hour >= 18 && Hour < 24, "Good Evening"
)
RETURN
Greeting & " " & SELECTEDVALUE(dimUser[First Name])
