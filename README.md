# Background Information

The Customer Experience Center (CEC) team plays a vital role in providing customer support and crop protection services through various channels, such as hotline, direct communication, and social media. This team also plays a crucial role in our organization's digital transformation strategy aimed at providing extensive support to underserved farmers, especially during economic downturns and crises. 

As the organization scales up its digital agronomy operations, there is a need to establish a robust data ecosystem to track and analyze key performance metrics, which will enable better decision-making and support the existing service channels. The organization's mission is to provide affordable farming technologies and knowledge to Myanmar's farmers, who lack access to resources that can help them adapt to a changing climate while their livelihoods directly depend on it. 

The project aligns with the organization's mission to empower farmers with the right information at the right time to protect their crops from adverse weather conditions and increased pestilence. Most farmers hear or learn about new farm practices and technologies during in-person exchanges. Unfortunately, the pandemic disrupted that routine. So, this project aims to shift from in-person to digital farming advisory, leveraging the growth of social media and information systems with intelligence feedback loops to provide timely and cost-effective farming advice directly to farmers' smartphones. 

# Project Deliverables
- Project charter [(See More)](https://bit.ly/43OcuA1)
- ETL workflow [(See More)](https://bit.ly/43Wj9s0)
- Data repository
- Wireframe
- Dashboard [(See More)](https://bit.ly/41tLZ1e)
- SOP
- User training
- Meeting notes


# ETL Workflow Diagrams

![](./Diagrams/Combine_Siloed_Data_with_OLAP.png)
> Combine Siloed Data with Production Data: At the start of this Customer Experience Center, the team used Google Sheets to capture information since the operation was in its early stages. Later on, with support from a centralized Business Intelligence and Data Analytics team, they designed and developed a comprehensive data ecosystem and reporting system. However, Google Sheets continued to be used initially to manage information before Digital Initiative team implement a digital twin of their operation

![](./Diagrams/ELT_process_from_Infobip_to_KNIME.png)
> Infobip to KNIME: Infobip is a leading provider of omnichannel communication, contact center, chatbot, customer engagement, and customer data platforms. Leveraging this cutting-edge technology, we share farming practice content with farmers via Viber on their smartphones. To ensure data safety and security, we transfer the data using SFTP, as recommended by Infobip. Then, we fetch this data and use it in our ETL pipeline for reporting purposes

![](./Diagrams/Messenger_LiveChat_Google_Sheet_Integration.png)
> Messenger LiveChat & Google Sheet Integration: ManyChat is a vital component of our digital agronomy solution as it enables us to disseminate farming advice to farmers and receive their direct inquiries via messenger. The platform's built-in integration facilitates the capture of chat flow and live chat data, which is then transmitted directly to Google Sheets. The Digital Marketing team established this integration during the early stages of development, while the Data team is responsible for managing and architecting this data to derive valuable insights from it. Although we plan to upgrade to a more advanced data storage solution, such as a Snowflake Cloud Data Warehouse, the current ETL pipeline represents our current workflow.

![](./Diagrams/Viber_Community_JSON_Parsing_Workflow.png)
> JSON from Viber Community: We have launched a digital farming practices community in Myanmar by utilizing the widespread coverage of Viber users. Through these communities, we educate farmers on the best practices to manage their farms, land, crops, pests & diseases, and yield. Viber serves as our platform to achieve these goals and connect with farmers.  The platform allows us to export JSON files, which contain daily-level-aggregated data that highlights the status of the community. The digital marketing team is responsible for launching new farming practices, educating, engaging, and maintaining these communities. They regularly download these JSON files and reached out our team (Data Analytics team) for leveraging the power of data and analytics to gain insights into community performance.


### DAX used in this SAMPLE Dashboard

```
# of Inquiry (Hotline) =
CALCULATE (
    SUM ( 'Inquiry Data [Hotline]'[# of Inquiry] )
)
```
```
# of Inquiry LY (Hotline) = 
CALCULATE(
    [# of Inquiry (Hotline)],
    SAMEPERIODLASTYEAR ( 'Master Data [Business Calendar]'[Date]))
```
```
# of Inquiry Solved (Hotline) =
CALCULATE (
    SUM ( 'Inquiry Data [Social Media]'[# of Inquiry] ),
    'Inquiry Data [Social Media]'[Status] = "Solved"
)
```
```
# of Inquiry (All Channels) = 
VAR Hotline = [# of Inquiry (Hotline)]
VAR SocialMedia = [# of Inquiry (Social Media)]

RETURN 
CALCULATE(Hotline + SocialMedia)
```

### SQL Query

```
select 
  a.InquiryID,
  b.CallSerialNo,
  b.UserName,
  b.UserPhone,
  b.Gender,
  b.Village,
  b.Township,
  b.State,
  d.InquiryTopic, 
  e.InquiryType, 
  c.Crop,
  b.CallType, 
  b.CallFrom, 
  b.CallStatus, 
  a.FirstHandlerStatus, 
  a.SecondHandlerStatus, 
  a.ProductandService, 
  a.ReasonForNoAction, 
  b.Remark, 
from 
  contactcenter_inquiries a 
  right join contactcenter_calls b on b.ID = a.CallID 
  left join contactcenter_cropmaster c on c.CropID = a.CropID 
  left join contactcenter_inquirytopic d on d.InquiryTopicID = a.InquiryTopicID 
  left join contactcenter_inquirytype e on e.InquiryTypeID = a.InquiryTypeID 
  left join contactcenter_referraltype f on f.ReferrelTypeID = a.ReferralTypeID 
  left join branches g on g.BranchID = a.ReferredBranchID

```

### Batch Script
```
C:\"Program Files"\KNIME\knime.exe -consoleLog -nosplash -application org.knime.product.KNIME_BATCH_APPLICATION -workflowFile=A:\Knime-workflow\automatedscripts\Name_of_Workflow.knwf -reset -nosave

if %errorlevel% == 0 (
goto :message0)
if %errorlevel% == 2 (
goto :message2)
if %errorlevel% == 3 (
goto :message3)
if %errorlevel% == 4 (
goto :message4)

:message0
@echo off
cls
echo ========= Workflow completed successfully ===========
pause
exit

:message2
@echo off
cls
echo ======== Parameters are wrong or missing ===========
pause
exit

:message3
@echo off
cls
echo ========= Errors occurs during loading the workflow ===========
pause
exit

:message4
@echo off
cls
echo ========= Workflow did not complete successfully =========== 
pause
exit
```

