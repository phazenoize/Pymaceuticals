# Pymaceuticals

Used chatgpt for help with this code:
 Calculate the final tumor volume of each mouse across four of the treatment regimens:  
 Capomulin, Ramicane, Infubinol, and Ceftamin
 Start by getting the last (greatest) timepoint for each mouse
 Merge this group df with the original DataFrame to get the tumor volume at the last timepoint
last_timepoint_per_mouse = cleaned_df.groupby(['Mouse ID'])['Timepoint'].max().reset_index()

 Merge this information with the original DataFrame to get the tumor volume at the last timepoint
merged_df = pd.merge(last_timepoint_per_mouse, cleaned_df, on=['Mouse ID', 'Timepoint'], how='left')

 Filter for the specified treatment regimens: Capomulin, Ramicane, Infubinol, and Ceftamin
specified_regimens = ['Capomulin', 'Ramicane', 'Infubinol', 'Ceftamin']
final_tumor_volume_df = merged_df[merged_df['Drug Regimen'].isin(specified_regimens)]
final_tumor_volume_df

 Calculate the mean tumor volume for each regimen
mean_tumor_volume_per_regimen = final_tumor_volume_df.groupby('Drug Regimen')['Tumor Volume (mm3)'].mean()
mean_tumor_volume_per_regimen = pd.DataFrame(mean_tumor_volume_per_regimen)
mean_tumor_volume_per_regimen.rename(columns={'Tumor Volume (mm3)':'Tumor Volume (mean)'}, inplace=True)
mean_tumor_volume_per_regimen = mean_tumor_volume_per_regimen.reset_index()

and this code:
 Put treatments into a list for for loop (and later for plot labels)
names = []
for treatments in mean_tumor_volume_per_regimen["Drug Regimen"].unique():
    names.append(treatments)

 Create empty list to fill with tumor vol data (for plotting)
tumor_vol = []

 Calculate the IQR and quantitatively determine if there are any potential outliers. 
for treatment in names:
    treatment_data = merged_df.loc[merged_df['Drug Regimen'] ==treatment, 'Tumor Volume (mm3)']
    tumor_vol.append(treatment_data.tolist())
    # Locate the rows which contain mice on each drug and get the tumor volumes  
    # add subset 
    q1 = treatment_data.quantile(0.25)
    q3 = treatment_data.quantile(0.75)
    IQR = q3 - q1

    # Determine outliers using upper and lower bounds
    lower_bound = q1 - 1.5 * IQR
    upper_bound = q3 + 1.5 * IQR
    outliers = treatment_data[(treatment_data < lower_bound) | (treatment_data > upper_bound)]
    num_outliers = len(outliers)
    print(f'\nTreatment: {treatment}')
    print(f'IQR: {IQR}')
    print(f'Lower Bound: {lower_bound}')
    print(f'Upper Bound: {upper_bound}')
    if num_outliers == 0:
        print('Potential Outliers: 0')
    else:
        print(f'Potential Outliers: {num_outliers}')
        print('Outliers:', outliers.tolist())

All other code was written by me.
