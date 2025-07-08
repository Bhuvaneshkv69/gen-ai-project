# gen-ai-project
# 1. Import Required Libraries
import pandas as pd
import os

# 2. Define Input and Output File Paths
input_file = "hotel_reviews.csv"
output_file = "review_prompts.csv"

# 3. Check if Input File Exists
if not os.path.exists(input_file):
    print(f"Error: {input_file} not found in this folder.")
    exit()

# 4. Load and Validate the Review Data
df = pd.read_csv(input_file)
if "Review" not in df.columns:
    print("Error: 'Review' column is missing in your CSV.")
    exit()
df.dropna(subset=["Review"], inplace=True)
df.reset_index(drop=True, inplace=True)
df["Review ID"] = df.index + 1

# 5. Define Prompt Template for Each Review
def create_prompt(review):
    review = review.strip()
    return f"""
You are a sentiment analysis AI.
Please analyze the customer review below, classify its sentiment (Positive,
Negative, or Mixed), and extract key topics or service areas mentioned.
Review: "{review}"
Sentiment:
Topics (comma-separated keywords):
"""

# 6. Apply the Prompt Template
df["Prompt"] = df["Review"].apply(create_prompt)

# 7. Export Final Results to CSV
final_df = df[["Review ID", "Review", "Prompt"]]
final_df.to_csv(output_file, index=False)
print(f"Prompts exported successfully to '{output_file}'")
