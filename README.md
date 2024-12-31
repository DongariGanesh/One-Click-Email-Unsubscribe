%%[

var @debug

var @jid

var @listid

var @batchid

var @email

var @skey

var @reason

var @unsubscribeAll

var @unsubscribe_date

var @emailname
 
set @debug = 0

set @jid = AttributeValue("jobid")

set @listid = AttributeValue("listid")

set @batchid = AttributeValue("_JobSubscriberBatchID")

set @email = AttributeValue("emailaddr")

set @skey = AttributeValue("_subscriberkey")

set @reason = "One-Click Unsubscribe"

set @unsubscribeAll = queryParameter("ua")

set @emailname = AttributeValue("emailname_")
 
if not empty(@skey) then

   var @lue

   var @lue_prop

   var @lue_statusCode

   var @overallStatus

   var @requestId

   var @Response

   var @Status

   var @Error
 
   if @unsubscribeAll == "1" then

     set @jid = ""

     set @listid = ""

     set @batchid = ""

   endif
 
   set @lue = CreateObject("ExecuteRequest")

   SetObjectProperty(@lue,"Name","LogUnsubEvent")
 
   set @lue_prop = CreateObject("APIProperty")

   SetObjectProperty(@lue_prop, "Name", "SubscriberKey")

   SetObjectProperty(@lue_prop, "Value", @skey)

   AddObjectArrayItem(@lue, "Parameters", @lue_prop)
 
   if not empty(@jid) then

    set @lue_prop = CreateObject("APIProperty")

    SetObjectProperty(@lue_prop, "Name", "JobID")

    SetObjectProperty(@lue_prop, "Value", @jid)

    AddObjectArrayItem(@lue, "Parameters", @lue_prop)

   endif
 
   if not empty(@listid) then

     set @lue_prop = CreateObject("APIProperty")

     SetObjectProperty(@lue_prop, "Name", "ListID")

     SetObjectProperty(@lue_prop, "Value", @listid)

     AddObjectArrayItem(@lue, "Parameters", @lue_prop)

   endif
 
   if not empty(@batchid) then

    set @lue_prop = CreateObject("APIProperty")

    SetObjectProperty(@lue_prop, "Name", "BatchID")

    SetObjectProperty(@lue_prop, "Value", @batchid)

    AddObjectArrayItem(@lue, "Parameters", @lue_prop)

   endif
 
   set @lue_prop = CreateObject("APIProperty")

   SetObjectProperty(@lue_prop, "Name", "Reason")

   SetObjectProperty(@lue_prop, "Value", @reason)

   AddObjectArrayItem(@lue, "Parameters", @lue_prop)
 
   set @lue_statusCode = InvokeExecute(@lue, @overallStatus, @requestId)
 
   set @Response = Row(@lue_statusCode, 1)

   set @Status = Field(@Response,"StatusMessage")

   set @Error = Field(@Response,"ErrorCode")

   var @updateDE

   set @status = "Inactive"

   set @reason = "One-Click Unsubscribe"

   set @updateDE = upsertDE("Test_Unsubscribe_Data_Capture", 1, 

                             "subscriberkey", @skey, 

                             "emailaddress", @email, 

                             "status", @status, 

                             "unsubscribe_date", NOW(), 

                             "jobid", @jid,

                             "batchid", @batchid,

                             "listid", @listid,

                             "reason", @reason,

                             "emailname", @emailname

                             )

endif
 
if @debug == 1 then

  output(concat("<br>jid: ", @jid))

  output(concat("<br>listid: ", @listid))

  output(concat("<br>batchid: ", @batchid))

  output(concat("<br>email: ", @email))

  output(concat("<br>skey: ", @skey))

  output(concat("<br>reason: ", @reason))

  output(concat("<br>unsubscribeAll: ", @unsubscribeAll))

  output(concat("<br>overallStatus: ", @overallStatus))

  output(concat("<br>requestId: ", @requestId))

  output(concat("<br>Response: ", @Response))

  output(concat("<br>Status: ", @Status))

  output(concat("<br>Error: ", @Error))

endif

]%%
 
