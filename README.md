



CREATE TABLE skapa_jobs.ci_type (
  ci_id BINARY(16) UNIQUE DEFAULT (uuid_to_bin(uuid())),
  ci_tool_name VARCHAR(20) PRIMARY KEY,
  created_timestamp TIMESTAMP NOT NULL DEFAULT current_timestamp
);

CREATE TABLE skapa_jobs.ci_team (
   ci_team_id BINARY(16) UNIQUE DEFAULT (uuid_to_bin(uuid())),
   ci_team_name VARCHAR(40) PRIMARY KEY,
   created_timestamp TIMESTAMP NOT NULL DEFAULT current_timestamp
);

CREATE TABLE skapa_jobs.tenant_names (
	tenant_id BINARY(16) UNIQUE DEFAULT (uuid_to_bin(uuid())),
	tenant_name VARCHAR(20),
	tenant_key VARCHAR(20) PRIMARY KEY,
	created_timestamp TIMESTAMP NOT NULL DEFAULT current_timestamp
 );
 
CREATE TABLE skapa_jobs.job_details (
   job_id BINARY(16) UNIQUE DEFAULT (uuid_to_bin(uuid())),
   job_name VARCHAR(255),
   job_health TEXT,
   job_health_description TEXT,
   team_name VARCHAR(40),
   job_owner VARCHAR(40),
   branch_name TEXT,
   last_build_result TEXT,
   last_build_timestamp BIGINT,
   last_build_url TEXT,
   last_build_tenant TEXT,
   user_comments TEXT,
   job_type VARCHAR(20),
   ci_name VARCHAR(20),
   embeddable_url TEXT,
   created_timestamp TIMESTAMP NOT NULL DEFAULT current_timestamp,
   PRIMARY KEY (job_name),
   FOREIGN KEY (team_name) REFERENCES skapa_jobs.ci_team(ci_team_name) ON DELETE CASCADE,
   FOREIGN KEY (ci_name) REFERENCES skapa_jobs.ci_type(ci_tool_name) ON DELETE CASCADE
);


CREATE TABLE skapa_jobs.job_build_history (
   build_history_id BINARY(16) UNIQUE DEFAULT (uuid_to_bin(uuid())),
   build_number INTEGER,
   job_id VARCHAR(255),
   build_result VARCHAR(15),
   build_duration BIGINT,
   build_url VARCHAR(255),
   build_timestamp BIGINT,
   branch_name TEXT,
   tenant_name VARCHAR(20),
   app_build_number TEXT,
   app_version TEXT,
   browser_name TEXT,
   build_stages JSON,
   created_timestamp TIMESTAMP NOT NULL DEFAULT current_timestamp,
   PRIMARY KEY(build_url,tenant_name),
   FOREIGN KEY (job_id) REFERENCES skapa_jobs.job_details(job_name) ON DELETE CASCADE,
   FOREIGN KEY (tenant_name) REFERENCES skapa_jobs.tenant_names(tenant_key) ON DELETE CASCADE
   );




CREATE TABLE skapa_jobs.job_test_project_mapping (
		id VARCHAR(255),
        job_name VARCHAR(255),
		ci_job_name VARCHAR(255),
		team_name VARCHAR(255),
        created_timestamp TIMESTAMP NOT NULL DEFAULT current_timestamp,
		PRIMARY KEY(job_name, ci_job_name)
)
   
CREATE TABLE skapa_jobs.job_test_project_execution (
		id VARCHAR(255),
        job_name VARCHAR(255),
		ci_job_name VARCHAR(255),
        build_result VARCHAR(20),
        build_timestamp VARCHAR(255),
        tc_pass_rate VARCHAR(100),
        tc_passed_count VARCHAR(100),
        tc_failed_count VARCHAR(100),
        tc_skipped_count VARCHAR(100),
		test_count VARCHAR(255),
        tenant_name VARCHAR(100),
        report_url VARCHAR(255),
		created_timestamp TIMESTAMP NOT NULL DEFAULT current_timestamp,
		PRIMARY KEY(report_url),
        FOREIGN KEY (job_name, ci_job_name) REFERENCES skapa_jobs.job_test_project_mapping(job_name, ci_job_name) ON DELETE CASCADE
   )


   CREATE TABLE skapa_jobs.test_project_script_mapping (
		id VARCHAR(255),
        script_name VARCHAR(255),
		job_name VARCHAR(255),
        ci_job_name VARCHAR(255),
		created_timestamp TIMESTAMP NOT NULL DEFAULT current_timestamp,
        PRIMARY KEY(script_name, job_name),
        FOREIGN KEY (job_name, ci_job_name) REFERENCES skapa_jobs.job_test_project_mapping(job_name, ci_job_name) ON DELETE CASCADE
   )
   
  

CREATE TABLE skapa_jobs.test_project_script_execution (
		id VARCHAR(255),
        script_name VARCHAR(255),
        job_name VARCHAR(255),
        build_result VARCHAR(20),
        build_timestamp VARCHAR(255),
        duration VARCHAR(255),
        target_count VARCHAR(100),
		tenant_name VARCHAR(100),
		created_timestamp TIMESTAMP NOT NULL DEFAULT current_timestamp,
		PRIMARY KEY(script_name, build_timestamp),
        FOREIGN KEY (script_name, job_name) REFERENCES skapa_jobs.test_project_script_mapping(script_name, job_name) ON DELETE CASCADE
   )

CREATE TABLE skapa_jobs.test_project_failed_steps (
		id MEDIUMINT NOT NULL AUTO_INCREMENT,
        step VARCHAR(255),
		script_name VARCHAR(255),
        job_name VARCHAR(255),
        ci_job_name VARCHAR(255),
		target_Name VARCHAR(255),
		build_timestamp VARCHAR(255),
		created_timestamp TIMESTAMP NOT NULL DEFAULT current_timestamp,
		PRIMARY KEY(id),
        FOREIGN KEY (script_name, build_timestamp) REFERENCES skapa_jobs.test_project_script_execution(script_name, build_timestamp) ON DELETE CASCADE,
        FOREIGN KEY (job_name, ci_job_name) REFERENCES skapa_jobs.job_test_project_mapping(job_name, ci_job_name) ON DELETE CASCADE
   )
