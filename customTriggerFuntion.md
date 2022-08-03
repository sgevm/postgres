```
DECLARE
  oldxmlbinary varchar;
  text_var1 text;
  text_var2 text;
  text_var3 text;  
BEGIN

  -- Save old value
  oldxmlbinary := get_xmlbinary();

  -- Change value base64 to ensure writing to _trigger_log is enabled
  SET LOCAL xmlbinary TO 'base64';
  
    INSERT INTO targetsalesforce.sf_sync__c(sync_id__c, object__c, source_id__c, id) VALUES(NEW.sfid, 'Case', NEW.sfid, nextval('targetsalesforce.sf_sync__c_id_seq'::regclass));

  -- Reset the value
  EXECUTE 'SET LOCAL xmlbinary TO ' || oldxmlbinary;

  RETURN NEW;
  
EXCEPTION WHEN others THEN  -- or be more specific
GET STACKED DIAGNOSTICS text_var1 = MESSAGE_TEXT,
                          text_var2 = PG_EXCEPTION_DETAIL,
                          text_var3 = PG_EXCEPTION_HINT;
                          
    INSERT INTO targetsalesforce.sync_error(error) VALUES (text_var1); -- identical table structure
    RETURN NULL;   -- cancel row  
END;

```
