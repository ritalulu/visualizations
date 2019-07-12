# Terms in Model Performance Matrix (TBI)

RMSE (Root Mean Square Error)
```r
calculate_fit_rmse = function(residuals){
   rmse=sqrt(sum((residuals)^2)/length(residuals))
   return(rmse)
```
SD.Error (Erros standard deviation)
```r
sd_error = sd(data_rdf$Actual - data_rdf$Predicted)
```
RMSE.Actual.change (RMSE for Actual values)
```r
rmse_actual = calculate_fit_rmse(data_rdf$Actual - data_rdf$True_today)
```
SD.Actual.change (Standard deviation for Actual values)
```r
sd_actual = sd(data_rdf$Actual - data_rdf$True_today)
```
MPE (mean percentage error)
```r
calculate_fit_mpe = function(actual, residuals){
  mape=mean(residuals/actual*100)
  sd_mape = sd(residuals/actual*100)
  return(c(mape, sd_mape))
}

mpe = calculate_fit_mpe(data_rdf$Actual, data_rdf$Actual - data_rdf$Predicted)

mpe[1]
```
SD.PE (Standard deviation for percentage error)
```r
mpe[2]
```
MAPE (mean absoluate percentage error)
```r
calculate_fit_mape = function(actual, residuals){
  mape=mean(abs(residuals/actual*100))
  sd_mape = sd(abs(residuals/actual*100))
  return(c(mape, sd_mape))
}
mape = calculate_fit_mape(data_rdf$Actual, data_rdf$Actual - data_rdf$Predicted)
mape[1]
```
SD.APE (Standard deviation for absoluate percentage error)
```r
mape[2]
```
Accuracy.2X2 (proportion of true results (both true positives and true negatives) for direction (up or down) among the total number of cases.)

```r
actual_diff = data_rdf$Actual - data_rdf$True_today
predicted_diff = data_rdf$Predicted - data_rdf$True_today
  
actual_log_diff = ifelse(actual_diff>0, "Up", "Down")
predicted_log_diff = ifelse(predicted_diff>0, "Up", "Down")
  
levels(actual_log_diff) <- list("Up" = "Up", "Down" = "Down")
levels(predicted_log_diff) <- list("Up" = "Up", "Down" = "Down")

direction_table = table(actual_log_diff, predicted_log_diff, dnn=c("Actual Values", "Predicted Values"))
confusion_Matrix = caret::confusionMatrix(direction_table)
accuracy_2_by_2 = as.data.frame(confusion_Matrix$overall)["Accuracy", ]
print(confusion_Matrix)
```
F1.score.Up (F1 score positive Up)

>The F1 score can be interpreted as a weighted average of the precision and recall, where an F1 score reaches its best value at 1 and worst score at 0. The relative contribution of precision and recall to the F1 score are equal. The formula for the F1 score is:
F1 = 2 * (precision * recall) / (precision + recall)
```r
f1_score_up = F1_Score(y_pred = predicted_log_diff, y_true = actual_log_diff, positive="Up")
```

F1.score.Down (F1 score positive Down)
```r
f1_score_down = F1_Score(y_pred = predicted_log_diff, y_true = actual_log_diff, positive="Down")
```

Accuracy.3X3.5. (Accuracy matrix percent=5%, True prediction proportion)
```r
accuracy_matrix = function(start_values, actual, predicted, percentage_accuracy = 0.1){
        N=length(predicted)
        PP=matrix(0,1,N)
        AP=matrix(0,1,N)
        E=matrix(0,1,N)
  
        A=as.vector(actual[1:length(actual)])
        P=as.vector(predicted)
        S = as.vector(start_values)
  
        #A=as.vector(actual[1:length(actual)])
        #P=as.vector(predicted[2:N])
        for (i in 1:N)
        {  
          #x=P[i]-A[i]
          #y=A[i+1]-A[i]
          
          x=(P[i]-S[i])/S[i]
          y=(A[i]-S[i])/S[i]
          
          if (x>percentage_accuracy) { PP[i]=1 } else if (x< -percentage_accuracy) {PP[i]=-1}   # indicator for up or down for prediction
          if (y>percentage_accuracy) { AP[i]=1 } else if (y< -percentage_accuracy) {AP[i]=-1}   # indicator for up/down for actual up/down
          #E[i]=PP[i]-AP[i]  # error of prediction up or down
        }
        #print(table(as.factor(E))/N)
        PP=factor(PP, levels=c(-1,0,1))
        AP=factor(AP, levels=c(-1,0,1))
        accuracy_table = as.matrix(table(PP,AP, useNA="always")/N)
        
        print(table(PP,AP)/N)
        #print(accuracy_table)
  
        good_accuracy = accuracy_table[1,1] + accuracy_table[2,2] + accuracy_table[3,3]
        bad_accuracy = accuracy_table[1,3] + accuracy_table[3,1]
      
        return(list(good_accuracy, bad_accuracy))
}
```
```r
accuracy_result_5 = accuracy_matrix(data_rdf$True_today, data_rdf$Actual, data_rdf$Predicted, percentage_accuracy = 0.05)
accuracy_result_5[[1]]
```
Bad.predictions.3X3.5. (Accuracy matrix percent=5%, false prediction proportion)

```r
accuracy_result_5[[2]]
```

Accuracy.3X3.2.5. (Accuracy matrix percent=2.5%, true prediction proportion)
```r
accuracy_result_2_5 = accuracy_matrix(data_rdf$True_today, data_rdf$Actual, data_rdf$Predicted, percentage_accuracy = 0.025)
accuracy_result_2_5[[1]]
```
Bad.predictions.3X3.2.5. (Accuracy matrix percent=2.5%, false prediction proportion)
```r
accuracy_result_2_5 [[2]]
```
F1_ave (average of F1.score.up and F1.score.down)

```r
ensemble$F1_ave = 0.5*as.numeric(as.character(ensemble$"F1.score.Up")) + 0.5*as.numeric(as.character(ensemble$"F1.score.Down"))
```
F1_ave7525 (weighted average, `0.75*F1.score.Up + 0.25*F1.score.Down`)

```r
ensemble$F1_ave7525 = 0.75*as.numeric(as.character(ensemble$"F1.score.Up")) + 0.25*as.numeric(as.character(ensemble$"F1.score.Down"))
```
RMSE_point (2 means 'in top 1 RMSE', 0 means not in)
```r
RMSE_top = top_n(ensemble, -num, RMSE) # Rita: `num` is a parameter in function `select_best_models_panamax `.

RMSE_top = RMSE_top[[1]]
ensemble$RMSE_point = ifelse(ensemble$variables %in% RMSE_top=='TRUE', 2, 0)
```

F1up_point (2 means 'in top 1 F1.score.Up', 0 means not in)
```r
F1up_top = top_n(ensemble, num, ensemble$"F1.score.Up")
F1up_top = F1up_top[[1]]
ensemble$F1up_point = ifelse(ensemble$variables %in% F1up_top=='TRUE', 2, 0)
```
F1down_point (2 means 'in top 1 F1.score.Down', 0 means not in)
```r
F1down_top = top_n(ensemble, num, ensemble$"F1.score.Down")
F1down_top = F1down_top[[1]]
ensemble$F1down_point = ifelse(ensemble$variables %in% F1down_top=='TRUE', 2, 0)
```

F1ave_point (1 means 'in top 1 F1_ave', 0 means not in)
```r
F1ave_top = top_n(ensemble, num, F1_ave)
F1ave_top = F1ave_top[[1]]
ensemble$F1ave_point = ifelse(ensemble$variables %in% F1ave_top=='TRUE', 1, 0)
```

F1ave7525_point (1 means 'in top 1 F1_ave7525', 0 means not in)
```r
F1ave7525_top = top_n(ensemble, num, F1_ave7525)
F1ave7525_top = F1ave7525_top[[1]]
ensemble$F1ave7525_point = ifelse(ensemble$variables %in% F1ave7525_top=='TRUE', 1, 0)
```

A3x3_point (1 means 'in top 1 Accuracy.3X3.5.', 0 means not in)
```r
A3x3_top = top_n(ensemble, num, ensemble$"Accuracy.3X3.5.")
A3x3_top = A3x3_top[[1]]
ensemble$A3x3_point = ifelse(ensemble$variables %in% A3x3_top=='TRUE', 1, 0)
```

B3x3_point (1 means 'in top 1 Bad.predictions.3X3.5.', 0 means not in)
```r
B3x3_top = top_n(ensemble, -num, ensemble$"Bad.predictions.3X3.5.")
B3x3_top = B3x3_top[[1]]
ensemble$B3x3_point = ifelse(ensemble$variables %in% B3x3_top=='TRUE', 1, 0)
```

points_total (sum of "RMSE_point", "F1up_point", "F1down_point", "F1ave_point", "A3x3_point", and "B3x3_point")
```r
ensemble$points_total = rowSums(ensemble[,c("RMSE_point", "F1up_point", "F1down_point", "F1ave_point", "A3x3_point", "B3x3_point")]) #EDIT October 12th 2018 removing "F1ave7525_point",  and adding "Percentage_right_point"
```
Top_models (1 means 'in top 1 points_total', 0 means not in)
```r
Points_top = top_n(ensemble, num, points_total)
Points_top = Points_top[[1]]
ensemble$Top_models = ifelse(ensemble$variables %in% Points_top=='TRUE', 1, 0)
```















