# bajajowner
#!/usr/bin/env python3
import requests
import json
import sys

def main():
    """
    Main function to orchestrate the flow of the application.
    1. Generate a webhook by sending a POST request
    2. Parse the response to get webhook URL and access token
    3. Determine which SQL problem to solve based on reg number
    4. Solve the SQL problem and get the final query
    5. Submit the solution to the webhook URL
    """
    print("Starting application...")
    
    # Your registration details
    registration_details = {
        "name": "Ram Patel",
        "regNo": "0827CY221049",
        "email": "rampatel220572@acropolis.in"
    }
    
    # Step 1: Generate webhook
    webhook_response = generate_webhook(registration_details)
    
    if not webhook_response:
        print("Failed to generate webhook. Exiting application.")
        sys.exit(1)
    
    # Step 2: Parse the response
    webhook_url = webhook_response.get("webhook")
    access_token = webhook_response.get("accessToken")
    
    if not webhook_url or not access_token:
        print("Invalid webhook response. Missing webhook URL or access token.")
        sys.exit(1)
    
    print(f"Webhook URL: {webhook_url}")
    print(f"Access Token: {access_token}")
    
    # Step 3: Determine which SQL problem to solve
    reg_number = registration_details["regNo"]
    last_digit = int(reg_number[-1])
    is_odd = last_digit % 2 != 0
    
    # Step 4: Solve the SQL problem
    final_query = solve_sql_problem(is_odd)
    
    # Step 5: Submit the solution
    submit_solution(webhook_url, access_token, final_query)

def generate_webhook(registration_details):
    """
    Generate webhook by sending a POST request.
    
    Args:
        registration_details (dict): Registration details including name, regNo, and email
    
    Returns:
        dict: Response containing webhook URL and access token
    """
    url = "https://bfhldevapigw.healthrx.co.in/hiring/generateWebhook/PYTHON"
    headers = {
        "Content-Type": "application/json"
    }
    
    try:
        response = requests.post(url, headers=headers, json=registration_details)
        response.raise_for_status()  # Raise an exception for 4XX/5XX responses
        
        return response.json()
    except requests.exceptions.RequestException as e:
        print(f"Error generating webhook: {e}")
        if hasattr(e, 'response') and e.response:
            print(f"Response: {e.response.text}")
        return None

def solve_sql_problem(is_odd):
    """
    Solve the SQL problem based on whether the registration number is odd or even.
    
    Args:
        is_odd (bool): True if the last digit of the registration number is odd, False otherwise
    
    Returns:
        str: The final SQL query solution
    """
    if is_odd:
        # Solution for Question 1 (odd registration number)
        # Based on the problem description, this would be the query for Question 1
        # This is a placeholder - replace with actual solution
        return """
SELECT 
    d.department_name, 
    COUNT(e.employee_id) AS employee_count,
    ROUND(AVG(e.salary), 2) AS avg_salary,
    MAX(e.salary) AS max_salary,
    MIN(e.salary) AS min_salary
FROM 
    employees e
JOIN 
    departments d ON e.department_id = d.department_id
GROUP BY 
    d.department_name
HAVING 
    COUNT(e.employee_id) >= 5
ORDER BY 
    avg_salary DESC;
"""
    else:
        # Solution for Question 2 (even registration number)
        # Based on the problem description, this would be the query for Question 2
        # This is a placeholder - replace with actual solution
        return """
SELECT 
    c.customer_name,
    COUNT(o.order_id) AS order_count,
    SUM(o.total_amount) AS total_spent,
    MAX(o.order_date) AS last_order_date
FROM 
    customers c
JOIN 
    orders o ON c.customer_id = o.customer_id
WHERE 
    o.order_date >= DATE_SUB(CURRENT_DATE(), INTERVAL 1 YEAR)
GROUP BY 
    c.customer_name
HAVING 
    COUNT(o.order_id) > 3
ORDER BY 
    total_spent DESC
LIMIT 10;
"""

def submit_solution(webhook_url, access_token, final_query):
    """
    Submit the solution to the webhook URL.
    
    Args:
        webhook_url (str): The webhook URL to submit the solution to
        access_token (str): The access token for authentication
        final_query (str): The final SQL query solution
    """
    headers = {
        "Authorization": access_token,
        "Content-Type": "application/json"
    }
    
    payload = {
        "finalQuery": final_query.strip()
    }
    
    try:
        response = requests.post(webhook_url, headers=headers, json=payload)
        response.raise_for_status()
        
        print("Solution submitted successfully!")
        print(f"Response: {response.text}")
        
        return response.json()
    except requests.exceptions.RequestException as e:
        print(f"Error submitting solution: {e}")
        if hasattr(e, 'response') and e.response:
            print(f"Response: {e.response.text}")
        return None

if _name_ == "_main_":
    main()
