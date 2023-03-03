# Install the required Azure PowerShell module if not already installed
if (-not (Get-Module -Name Az.Resources -ListAvailable)) {
    Install-Module -Name Az.Resources -Scope CurrentUser -Force
}

# Set the output CSV file path
$outputFilePath = "policy-data.csv"

# Get all Azure subscriptions
$subscriptions = Get-AzSubscription | Select-Object SubscriptionId

# Create an empty array to hold the combined policy data
$combinedData = @()

# Loop through each subscription and export its policy data
foreach ($subscription in $subscriptions) {
    $subscriptionId = $subscription.SubscriptionId

    # Set the current subscription context
    Set-AzContext -Subscription $subscriptionId | Out-Null

    # Get all policy definitions
    $policyDefinitions = Get-AzPolicyDefinition | Select-Object Name, Description, PolicyType, Mode, Metadata

    # Get all policy states
    $policyStates = Get-AzPolicyState | Select-Object PolicyDefinitionName, DisplayName, Description, PolicyDefinitionReferenceId, ResourceId, PolicySetDefinitionName, PolicyAssignmentId, PolicySetDefinitionId, PolicyDefinitionId, SubscriptionId, IsCompliant, EnforcementMode, CreatedOn, LastUpdatedOn, PolicyDecision, AdditionalProperties

    # Add the subscription ID to each row in both datasets
    $policyDefinitions | Add-Member -NotePropertyName "SubscriptionId" -NotePropertyValue $subscriptionId -PassThru |
    ForEach-Object {
        $policyDefName = $_.Name
        $matchingPolicyState = $policyStates | Where-Object { $_.PolicyDefinitionName -eq $policyDefName } | Select-Object -First 1

        # Combine the policy definition and policy state data
        $combinedData += $_ | Select-Object *, @{Name="DisplayName";Expression={$matchingPolicyState.DisplayName}}, @{Name="IsCompliant";Expression={$matchingPolicyState.IsCompliant}}, @{Name="EnforcementMode";Expression={$matchingPolicyState.EnforcementMode}}, @{Name="CreatedOn";Expression={$matchingPolicyState.CreatedOn}}, @{Name="LastUpdatedOn";Expression={$matchingPolicyState.LastUpdatedOn}}, @{Name="PolicyDecision";Expression={$matchingPolicyState.PolicyDecision}}, @{Name="ResourceId";Expression={$matchingPolicyState.ResourceId}}
    }
}

# Export the combined data to a CSV file
$combinedData | Export-Csv -Path $outputFilePath -NoTypeInformation


This script should loop through all Azure subscriptions, and for each subscription, get the policy definitions and policy states. 
It will then combine the two datasets on the PolicyDefinitionName column and add some additional properties from the policy states dataset. 
Finally, it will export the combined data to a CSV file named "policy-data.csv".
