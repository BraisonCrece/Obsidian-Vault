## <span style='color:#20bf6b'>end2end-test</span>
			--  database
            --  mailhog
            --  chrome
            --  nginx-e2e-test
            --  fake-twilio-server-test

### <span style='color:#20bf6b'>test</span> 
			--  database
		    --  redis
		    --  mailhog
		    --  chrome
		    --  fake-bank-soap-server
		    --  fake-iban-validation-server
		    --  fake-idnow-server-test
		    --  fake-address-validation-server
		    --  fake-sftp-file-server
		    --  fake-twilio-server-test
		    --  performance-service

### <span style='color:#20bf6b'>web-test </span>
			--  database
	        --  redis
	        --  fake-bank-soap-server
	        --  fake-iban-validation-server
	        --  fake-idnow-server-test
	        --  fake-address-validation-server
	        --  fake-twilio-server-test
	        --  mailhog

### <span style='color:#20bf6b'>nginx-e2e-test </span>
			--  web-test
            --  frontend-test

### <span style='color:#20bf6b'>frontend-test</span> 
			--  web-test

### <span style='color:#8854d0'>nginx</span> 
			--  web
		    --  frontend

### <span style='color:#8854d0'>migration-service</span> 
			--  database
            --  redis

### <span style='color:#8854d0'>worker </span>
			--  database
			--  redis
			--  image-service
			--  performance-service
			--  projection-engine
			--  mailhog
			--  proxy-service

### <span style='color:#8854d0'>web </span>
			--  database
			--  redis
			--  image-service
			--  performance-service
			--  projection-engine
			--  fake-idnow-server
			--  fake-twilio-server
			--  fake-address-validation-server
			--  mailhog
			--  worker
			--  webpack

### <span style='color:#8854d0'>performance-service</span> 
			--  database

### <span style='color:#8854d0'>projection-engine </span>
			--  proxy-service
