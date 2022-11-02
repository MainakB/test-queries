# test-queries

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
