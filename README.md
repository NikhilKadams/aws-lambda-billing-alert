# AWS Lambda: Monitor and Alert High AWS Billing

## Overview

This AWS Lambda function monitors your AWS billing metrics and sends an SNS email alert when your estimated charges exceed a specified threshold. It helps manage your AWS costs proactively by notifying you of high spending.

---

## Steps Followed

### 1. SNS Topic Setup

- Created an SNS topic named `AwsBillingAlerts`.
- Subscribed to the topic with an email endpoint.
- Confirmed subscription via email.

### 2. IAM Role Creation

- Created an IAM role `LambdaBillingAlertRole` for Lambda.
- Attached the following managed policies:
  - `CloudWatchReadOnlyAccess` — To read billing metrics.
  - `AmazonSNSFullAccess` — To publish SNS notifications.

### 3. Lambda Function Development

- Created a Lambda function named `AWSBillingAlert` using Python 3.x runtime.
- Assigned the `LambdaBillingAlertRole` IAM role.
- Implemented code to:
  - Retrieve AWS EstimatedCharges metric from CloudWatch for the last day.
  - Compare the estimated charge to a `$50` threshold.
  - Send an SNS alert if the threshold is exceeded.

### 4. Testing

- Created and ran a manual test event with empty JSON `{}`.
- Reviewed CloudWatch logs for billing data or notification info.

---

## Usage Instructions

- Update the `SNS_TOPIC_ARN` constant in the code with your SNS topic ARN.
- Adjust `BILLING_THRESHOLD` as needed.
- Deploy the Lambda function with the appropriate IAM role.
- Optionally, schedule the function to run daily with Amazon EventBridge (CloudWatch Events).
- Monitor CloudWatch logs and your email inbox for alerts.

---

## Code Snippet

response = cloudwatch.get_metric_statistics(
    Namespace='AWS/Billing',
    MetricName='EstimatedCharges',
    Dimensions=[{'Name': 'Currency', 'Value': 'USD'}],
    StartTime=start_time,
    EndTime=end_time,
    Period=86400,
    Statistics=['Maximum']
)

datapoints = response.get('Datapoints', [])
if not datapoints:
    print("No billing data available for the last 24 hours.")
    return

max_charge = max(dp['Maximum'] for dp in datapoints)
print(f"Maximum estimated charge in the past 24 hours: ${max_charge:.2f}")

if max_charge > BILLING_THRESHOLD:
    message = f"Alert! AWS estimated charges exceeded the threshold of ${BILLING_THRESHOLD:.2f}. Current charge: ${max_charge:.2f}."
    sns.publish(
        TopicArn=SNS_TOPIC_ARN,
        Message=message,
        Subject="AWS Billing Alert"
    )
    print("Billing alert sent.")
else:
    print("Billing is within threshold.")

---

## Screenshots

Include screenshots illustrating:

- Lambda code editor.
- CloudWatch logs confirming execution.
- SNS subscription email confirmation.

---

## Summary

This automation script uses AWS Lambda, CloudWatch metrics, and SNS to monitor AWS billing and notify stakeholders proactively about high charges, helping control AWS costs effectively.
# AWS Lambda: Monitor and Alert High AWS Billing

## Overview

This AWS Lambda function checks the AWS billing metrics daily and sends an SNS email alert when your estimated charges exceed a defined threshold. It helps proactively monitor and control your AWS costs.

---

## Steps Followed

1. Created an SNS topic (`AwsBillingAlerts`) and subscribed an email to receive alerts.
2. Created an IAM role (`LambdaBillingAlertRole`) with `CloudWatchReadOnlyAccess` and `AmazonSNSFullAccess`.
3. Created a Lambda function using Python 3.x runtime, assigned the above IAM role.
4. Implemented code to:
   - Fetch AWS billing metrics from CloudWatch.
   - Compare charges against a threshold (e.g., $50).
   - Send an SNS alert if threshold is exceeded.
5. Tested the function with a manual empty test event.
6. Verified CloudWatch logs to confirm execution.

---

## Usage Instructions

- Replace `SNS_TOPIC_ARN` in the Lambda code with your SNS topic ARN.
- Adjust the `BILLING_THRESHOLD` as needed.
- Deploy your Lambda function with the correct IAM role.
- Optionally schedule the function to run daily via EventBridge.
- Monitor CloudWatch logs and email alerts.

---

## Code Snippet

response = cloudwatch.get_metric_statistics(
    Namespace='AWS/Billing',
    MetricName='EstimatedCharges',
    Dimensions=[{'Name': 'Currency', 'Value': 'USD'}],
    StartTime=start_time,
    EndTime=end_time,
    Period=86400,
    Statistics=['Maximum']
)

datapoints = response.get('Datapoints', [])
if not datapoints:
    print("No billing data available for the last 24 hours.")
    return

max_charge = max(dp['Maximum'] for dp in datapoints)
print(f"Max estimated charge in last 24 hours: ${max_charge:.2f}")

if max_charge > BILLING_THRESHOLD:
    message = (f"Alert! Estimated AWS charges exceeded ${BILLING_THRESHOLD:.2f}.\n"
               f"Current estimate: ${max_charge:.2f}.")
    sns.publish(
        TopicArn=SNS_TOPIC_ARN,
        Message=message,
        Subject="AWS Billing Alert"
    )
    print("Billing alert sent.")
else:
    print("Billing is within threshold.")

---

## Screenshots

Include:

- Lambda code in AWS Console.
- CloudWatch logs with execution results.
- SNS confirmation email.

---

## Summary

This Lambda function automates AWS billing monitoring and sends timely alerts to help manage costs effectively.
