מטרה כללית 
הפרויקט נועד לבדוק את היכולת שלכם ליישם באופן עצמאי ומקצועי את רוב הכלים והחשיבה 
הסטטיסטית שנלמדו בקורס על מערך נתונים אמיתי  – מהשאלה ועד למסקנות בעלות משמעות. 
נושא ונתונים  
• כל סטודנט מקבל נושא מוגדר מראש + קובץ נתונים אחד   
• אין אפשרות לבחור נושא אחר או להחליף / להוסיף קובץ נתונים  
מה נדרש להגיש 
• מסמך / קובץ / מצגת / דוח  – בכל פורמט שנראה לכם מתאים  
• המטרה היא להראות הבנה עמוקה, חשיבה סטטיסטית נכונה, קבלת החלטות מושכלות 
ויכולת להעביר מסר ברור   
• אין מבנה חובה – אתם מחליטים איך לארגן את החומר (רצף לוגי, סיפור, דוח קלאסי, מצגת 
מורחבת, נוטבוק עם הסברים וכו')  
אלמנטים שכדאי שיופיעו (בצורה כלשהי)  
• הצגת השאלה / המטרה העסקית-מחקרית  
• תיאור ראשוני של הנתונים (גודל, סוגי משתנים, בעיות בולטות)  
• מה עשיתם עם הנתונים לפני הניתוח (חסרים? חריגים? טרנספורמציות? משתנים חדשים?)   
• אילו שיטות ניתוח בחרתם  – ולמה דווקא אותן   
• תוצאות עיקריות (טבלאות / גרפים / מדדים)   
• אבחון ואימות (שאריות? הנחות? השוואת מודלים? over-fitting  )? 
• מסקנות ישירות לשאלה + פרשנות בעלת משמעות   
• מגבלות + מה לא הצלחתם לעשות / מה היה יכול לשפר את העבודה 
דרישות טכניות מינימליות  
• קוד נקי ומתועד (Python או R או שילוב) – חייב להיות ניתן להריץ אותו   
• גרפים קריאים עם כותרות ותוויות   
• טבלאות מסודרות (לא צילומי מסך מבולגנים)  
 
 
קריטריוני הערכה עיקריים  
• טיפול מעמיק ומנומק בנתונים + EDA איכותי   
• בחירה, יישום ואבחון מודלים  
• השוואה בין מודלים והסקת מסקנות מבוססות   
• פרשנות עסקית/מדעית + מודעות למגבלות  
• איכות הכתיבה, הויזואליזציה והארגון   
 
הפרויקט הזה הוא המרכיב המרכזי של הציון בקורס.  
ועד לדמות עבודה אמיתית של מדען/ת נתונים –  מהשאלה ועד ההמלצה המעשית.
import pandas as pd
import numpy as np
import statsmodels.api as sm
from statsmodels.stats.outliers_influence import variance_inflation_factor

# --- Data Preparation ---
# Define Target: Serious Injury
serious_mask = ['FATAL INJURY', 'SUSPECTED SERIOUS INJURY']
df['Is_Serious'] = df['Injury Severity'].isin(serious_mask).astype(int)

# Select predictors and handle missing values
cols_to_use = ['Weather', 'Surface Condition', 'Light']
model_df = df[cols_to_use + ['Is_Serious']].copy()

# Simple cleaning: group unknown/missing into the most frequent category
for col in cols_to_use:
    most_freq = model_df[col].mode()[0]
    model_df[col] = model_df[col].replace(['Missing/Unknown', 'UNKNOWN', 'OTHER'], most_freq)

# --- Create Model Matrix (The Robust Way) ---
# This replaces the formula 'Is_Serious ~ C(Weather) + ...'
X = pd.get_dummies(model_df[cols_to_use], drop_first=True).astype(float)
X = sm.add_constant(X) # Don't forget the intercept!
y = model_df['Is_Serious']

# --- Fit Model 1 ---
logit_mod1 = sm.Logit(y, X).fit()
print(logit_mod1.summary())

# --- Model Comparison (Requirement C) ---
# Create a "Reduced Model" (e.g., without Weather) to compare
X_reduced = X.drop([c for c in X.columns if 'Weather' in c], axis=1)
logit_mod_reduced = sm.Logit(y, X_reduced).fit()

print(f"\nFull Model AIC: {logit_mod1.aic}")
print(f"Reduced Model AIC: {logit_mod_reduced.aic}")
print(f"Difference in AIC: {logit_mod_reduced.aic - logit_mod1.aic}")
# Interpretation: Lower AIC is better.


# 1. Prepare data: Count crashes per Day and Hour
poisson_df = df.groupby(['Year', 'Month', 'Day_of_Week', 'Hour']).size().reset_index(name='Crash_Count')

# 2. Fit Poisson Model
poisson_mod = smf.poisson('Crash_Count ~ C(Day_of_Week) + Hour', data=poisson_df).fit()

print("\n--- Poisson Regression Summary ---")
print(poisson_mod.summary())


from sklearn.ensemble import RandomForestClassifier
from sklearn.preprocessing import LabelEncoder
# 1. Clean Data for RF (Handle outliers in Vehicle Year)
rf_df = df[(df['Vehicle Year'] > 1990) & (df['Vehicle Year'] < 2025)].copy()
rf_df = rf_df[rf_df['Vehicle Damage Extent'] != 'Missing/Unknown'].dropna(subset=['Vehicle Damage Extent'])
# 2. Encode categorical variables
le = LabelEncoder()
rf_df['Damage_Target'] = le.fit_transform(rf_df['Vehicle Damage Extent'])
# Features: Mixture of numeric and categorical (encoded)
features = ['Vehicle Year', 'Speed Limit', 'Weather_Simple', 'Surface_Simple']
X_rf = pd.get_dummies(rf_df[features], drop_first=True)
y_rf = rf_df['Damage_Target']
# 3. Fit Random Forest
rf = RandomForestClassifier(n_estimators=100, max_depth=10, random_state=42)
rf.fit(X_rf, y_rf)
# 4. Plot Feature Importance (Requirement D)
import matplotlib.pyplot as plt
importances = pd.Series(rf.feature_importances_, index=X_rf.columns).sort_values()
plt.figure(figsize=(10, 6))
importances.plot(kind='barh', color='teal')
plt.title("Feature Importance: What predicts Vehicle Damage?")
plt.xlabel("Importance Score")
plt.show()

# Calculate Odds Ratios
odds_ratios = np.exp(logit_mod1.params)
conf = np.exp(logit_mod1.conf_int())
conf.columns = ['Lower 95%', 'Upper 95%']

or_table = pd.DataFrame({
    'Odds Ratio': odds_ratios,
    'Lower 95%': conf['Lower 95%'],
    'Upper 95%': conf['Upper 95%']
})

print("\n--- Odds Ratios and Confidence Intervals ---")
print(or_table)

# Calculate VIF for the model matrix (excluding the constant)
vif_data = pd.DataFrame()
X_vif = X.drop('const', axis=1)
vif_data["feature"] = X_vif.columns
vif_data["VIF"] = [variance_inflation_factor(X_vif.values, i) for i in range(len(X_vif.columns))]

print("\n--- Variance Inflation Factor (VIF) ---")
print(vif_data.sort_values(by="VIF", ascending=False))
