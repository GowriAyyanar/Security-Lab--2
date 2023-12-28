# Systems Hardening with Patch Manager via AWS Systems Manager

## Objectives
After completing this lab, you should be able to:

- Create a custom patch baseline
- Modify patch groups
- Configure patching
- Verify patch compliance

## Task 1: Select patch baselines
1. In this task, you select a patch baseline to apply to the Linux EC2 instances. You then create a custom patch baseline for the Windows server EC2 instances.
2. Patch Manager provides predefined patch baselines for each of the operating systems that it supports. You can use these baselines as they are currently configured (you can't customize them), or you can create your own custom patch baselines. You can use custom patch baselines for greater control over which patches are approved or rejected for your environment.
3. In the AWS Management Console, in the search  box, enter Systems Manager and select it. This option takes you to the Systems Manager console page.
4. In the left navigation pane, under Node Management, choose Fleet Manager.
5. Here are the pre-configured EC2 instances. There are three Linux instances and three Windows instances. These EC2 instances have a specific IAM role associated with them that allows you you managed them using Systems Manager, which is why you can view them in the Fleet Manager section. This is also part of the lab setup.
6. Select the check box next to Linux-1. Then choose the Node actions  dropdown list, and choose View details.
7. Here you can view details about the specific instance, such as Platform type, Node type, OS name, and the IAM role that allows you to use Systems Manager to manage this instance.
8. At the top of the page, choose AWS Systems Manager to go back to the Systems Manager homepage.
9. In the left navigation pane, under Node Management, choose Patch Manager.
10. Choose Start with an overview (Proceed to next step if this option does not appear).
11. Choose the Patch baselines tab. This tab includes the default patch baselines that you can select.
12. In the search bar, enter AWS-AmazonLinux2DefaultPatchBaseline and press Enter. Then select the radio button  next to the baseline that is listed.
13. Choose the Actions  dropdown list, and choose Modify patch groups.
14. You can use a patch group to associate managed nodes with a specific patch baseline in Patch Manager. Patch groups help ensure that you're deploying the appropriate patches based on the associated patch baseline rules to the correct set of nodes.
15. In the Modify patch groups section under Patch groups, enter LinuxProd and then choose the Add button.
16. Choose Close.

## Task 1.1: Tag instances
1. In this task, you tag your Windows instances. Later in the lab, you create a patch group and associate it with these tags. 
2. The Linux instances were pre-configured during lab setup with LinuxProd tags and do not need any added tags.
3. In the AWS Management Console, in the search  bar, enter EC2 and select it.
4. Choose Instances, select the check box next to the Windows-1 instance, and then choose the Tags tab. 
5. Choose the Manage tags button, choose Add new tag, and configure the following options:
6. Key: Enter Patch Group
7. Value: Enter WindowsProd
8. Choose Save.
9. Repeat the previous steps to tag the Windows-2 and Windows-3 instances with the same tag.

## Task 1.2: Create a custom patch baseline
1. Next, you create a custom patch baseline for the Windows instances. Although Windows has default patch baselines that you can use, for this use case, you set up a baseline for Windows security updates.
2. Return to the Systems Manager console. In the search bar at the top, enter Systems Manager and then select it.
3. In the left navigation pane, under Node Management, choose Patch Manager.
4. Choose Start with an overview (Proceed to next step if this option does not appear).
5. Choose the Patch baselines tab.
6. Choose the Create patch baseline button. 
7. For Patch baseline details, configure the following options:

- For Name, enter WindowsServerSecurityUpdates
- For Description - optional, enter Windows security baseline patch
- For Operating system, choose Windows.
- Leave the check box for Default patch baseline unselected.
8. In the Approval rules for operating systems section, configure the following options:
- Products: From the dropdown list, choose WindowsServer2019. Also, deselect All so that it no longer appears under Products.
- Severity: This option indicates the severity value of the patches that the rule applies to. To ensure that all service packs are included by the rule, choose Critical from the dropdown list.
- Classification: From the dropdown list, choose SecurityUpdates.
- Auto-approval: Enter 3 days.
- Compliance reporting - optional: From the dropdown list, choose Critical.
9. Choose Add rule to add a second rule to this patch baseline, and configure the following options:
- Products: From the dropdown list, choose WindowsServer2019. Also, deselect All so that it no longer appears under Products.
- Severity: From the dropdown list, choose Important.
- Classification: From the dropdown list, choose SecurityUpdates.
- Auto-approval: Enter 3 days.
- Compliance reporting - optional: From the dropdown list, choose High.
10. Choose Create patch baseline.
Next, modify a patch group for the Windows patch baseline that you just created, to associate it with a patch group.

11. In the Patch baselines section, select the button for the WindowsServerSecurityUpdates patch baseline that you just created.
Note: The patch baseline that you created may be on the second page of the baselines list. You could also use the search bar to locate it and then select it.
12. Choose the Actions dropdown list, and then choose Modify patch groups.
13. In the Modify patch groups section under Patch groups, enter WindowsProd 
14. Choose the Add button, and then choose Close. 

Summary of task 1
In this task, you used a default Linux Amazon patch baseline and modified a patch group for the LinuxProd group. You then tagged your Windows instances so that they could be associated with the WindowsProd patch group. You learned how to create a custom patch baseline for the Windows instances. 

## Task 2: Configure patching
In this task, you patch the Linux instances using the Patch now feature and by specifying the key/value pair that the Linux instances are all tagged with. You then patch the Windows instances, also based on the tag associated with them.
After configuration, Patch Manager uses the Run Command to call the RunPatchBaseline document to evaluate which patches should be installed on target instances according to each instance's operating system type directly or during the defined schedule (maintenance window).

## Task 2.1 Patch the Linux instances
1. Patch the Linux instances.
- From the Patch Manager console page, choose Patch now.
- Patching operation:  Scan and install
- Reboot option: Reboot if needed
- Instances to patch: Patch only the target instances I specify
- Target selection: Specify instance tags
- Tag key:  Patch Group
- Tag value: LinuxProd
- Choose Add
- Choose Patch now
2. Observe the status of the patching.
A new page displays. In the AWS-PatchNowAssociation panel, there is a Progress field that will show that three instances will be affected and the progress made.
A Scan/Install operation summary panel also displays the status of the affected EC2 instances visually. Monitor this page until the patch operation on all three instances completes.

## Task 2.2: Patch the Windows instances
1. Patch the Windows instances.
- Choose Patch Manager and then choose Patch now.
- Patching operation:  Scan and install
- Reboot option: Reboot if needed
- Instances to patch: Patch only the target instances I specify
- Target selection: Specify instance tags
- Tag key:  Patch Group
- Tag value: WindowsProd
- Choose Add
- Choose Patch now
2. A new page displays. When it becomes available, choose the link to the Execution ID.
A page in the State Manager part of Systems Manager opens.
3. Choose the Output link for one of the managed instances that shows a status of InProgress.
A page in the Run Command part of Systems Manager opens.
4. Expand the Output panel to observe the details.
Behind the scenes, Patch Manager uses the Run Command to run the PatchBaselineOperations.  If you scroll through the output, you should see the PatchGroup: WindowsProd details.
A Systems Manager document (SSM document) defines the actions that Systems Manager performs on your managed instances.

## Task 2.2: Verify compliance
1. In the left navigation pane, under Node Management, choose Patch Manager.
2. Choose the Dashboard tab. Under Compliance summary, you should now see Compliant: 6, which verifies that all Windows and Linux instances are compliant.
3. Choose the Compliance reporting tab. 
 This tab provides an overview of all running instances with SSM. You should be able to verify that the Compliance status of all Linux and Windows instances is Compliant.
- All six instance (three Linux and three Windows) should show as compliant.
- Scroll to the right in the Node patching details panel to find for each instance:
- Critical noncompliant count
- Security noncompliant count
- Other noncompliant count
- Last operation date 
- Baseline ID 
- Choose the Node ID for one of the Windows nodes.
- In the Node ID page that opens, choose the Patch tab.
- Scroll down and observe what patches were applied to this instance, as well as the Installed Time.

## Summary of task 2
In this task, you configured patching and patched instances in both the the LinuxProd group and the WindowsProd group. 

You learned how to scan and install patches instantly and analyze the output from the Run command to see the patch updates. You verified through compliance reporting that all EC2 instances have been scanned and updated and are compliant. 

## Conclusion
 Congratulations! You now have successfully:

Created a custom patch baseline
Modified two patch groups
Configured patching
Verified patch compliance
