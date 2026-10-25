Foreign Key Delete Policies



1\. Student–Application Relationship



Foreign key: application.student\_id references student.student\_id



Policy: ON DELETE NO ACTION (default PostgreSQL behavior).



Reason: A student with existing applications should not be deleted while those applications reference the student. This protects application history.



Why not CASCADE? CASCADE would automatically delete the student's applications, which could remove useful application history.



2\. Internship–Application Relationship



Foreign key: application.internship\_id references internship.internship\_id



Policy: ON DELETE NO ACTION (default PostgreSQL behavior).



Reason: An internship with existing applications should not be deleted while applications reference it.



Why not SET NULL? The internship\_id column is required and cannot be NULL. An application without an associated internship would also lose important context.



3\. Company–Internship Relationship



Foreign key: internship.company\_id references company.company\_id



Policy: ON DELETE NO ACTION (default PostgreSQL behavior).



Reason: A company with existing internship listings should not be deleted until those listings are handled. This protects the relationship between companies and internships.



Why not CASCADE? Deleting a company could automatically remove its internship listings and affect related applications.



RESTRICT vs. NO ACTION



RESTRICT prevents a referenced row from being deleted when dependent rows exist, while NO ACTION checks referential integrity at constraint-check time and can allow deferred checking when the constraint is deferrable.



CASCADE Evidence



The current schema uses the default NO ACTION behavior for its foreign keys, so deleting a referenced company is expected to fail with SQLSTATE 23503. The following commands demonstrate the existing behavior and record the number of internships before and after the attempted deletion.



SELECT COUNT(\*) AS internships\_before

FROM internship

WHERE company\_id = 1;



DELETE FROM company

WHERE company\_id = 1;



SELECT COUNT(\*) AS internships\_after

FROM internship

WHERE company\_id = 1;



The DELETE statement is expected to fail because the company is referenced by internship rows. Run the final SELECT separately after the failed DELETE to confirm that the internship count remains unchanged.



This demonstrates that the current foreign key policy preserves related internship records rather than deleting them automatically.

