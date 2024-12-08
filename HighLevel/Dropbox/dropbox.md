Dropbox

# Requirements

## Functional Requirements

1. Users can upload a file from any device
2. Users can download a file from any device
3. Users can share files to other users can assign the permissions and rights
4. Files shoudl sync across devices
5. [Optional]Allow to edit the files
6. [Optional]In browser for the files

## Non Functional Requirements

1. highly available
2. Support large file upto 50 GB
3. recover last snapshots of lost files
4. Low latency syncs / uploads/ downloads
5. Storage limits for the user
6. [Optional]Support file versioning
7. [Optional]Scan for viruses and malware

## Capacity Estimation


Number of Users = 500 milliom
Daily active user = 100 million
?? to defined i dont think we are impacted much if we know we have to build reliable available solution


# Core Entities

1. Users
2. File
3. File Metadata (file name, size, mime type, users, )

# API Design

1. POST file/ , request file(presigned url), fileMetadta
2. GET  file/{fileid}, file
3. POST files/{fileid}/share , request users[]
4. POST files/{fileID}/changes, response metadata
5. POST file/search, request keywords, name

# High Level Design

1. upload files

files can be uiploaded to the object store s3 bucket
for the file metadata, we can use NOSQl database and metadata is always subject to a change

now we can use presigned urls and upload directly to the blob storage to client, a little slower, but less impact on the rest of the flow through the network
and can be stored and passed throught the system. This presigned url also contains the auth information, you can also control share access throigh this urls, including expirations and timeouts



2. download files / share files to other users
we can download from the object store, using the presigned urls. plus to maintain a list of who is shared to who. we have another database( simple sql would do), to have the userid vs file id
to get the required file.

This table / information can be geographically cached / CDN for the user as well, as they are chance user might use at the same place all the time.


uploads and downloads can also benefit from chunking , during the upload, so failure of networ on either side the customer or the dropbox, users has to start all over again. This is supported by S3 in itself


3. File syncs

file sync from the client side to the cloud / online storage, could monitor the changes in the folder that is allowed to checked/seen by the user.
and periodically any new filed can be queued for the upload accordingly

usually edit and uploaded the same file, the last user to do that wins, or last write wins

the same client can also poll for the newer files from the server, now this can be a manual process to click on a sync button. or a time specificed by the customer to request the server in the time.
this is the easiest way to implement it rather than keeping an open connection to the server all the time, which increases a load to the server for no reason

we can also have indicator for the fresh and stale versions of the files, the client side may know that the server has new updated timestamp for the file, but it isnt downloaded or synced yet


4. File Security

Encryption in transit and at rest when stored on the server as well