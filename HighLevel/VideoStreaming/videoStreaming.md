Video Streaming

Netflix / youtube


# Requirements


## Functional Requirements

Users can upload videos.
Users can watch (stream) videos.

[optional]
Users can view information about a video, such as view counts.
Users can search for videos.
Users can comment on videos.
Users can see recommended videos.
Users can make a channel and manage their channel.
Users can subscribe to channels.
Channel Analytics


## Non Functional Requirements

The system should be highly available (prioritizing availability over consistency).
The system should support uploading and streaming large videos (10s of GBs).
The system should allow for low latency streaming of videos, even in low bandwidth environments.
The system should scale to a high number of videos uploaded and watched per day (~1M videos uploaded per day, 100M videos watched per day).
The system should support resumable uploads.


## Core Entities

Users
Videos
Videos Metadata
Analytics (User / Channels / Videos / Videos Metadata)


# API Design

POST video/upload
GET /video/{videoId}
Search /video, Request->{
    information
}

GET /Analytics/users/{userid}
GET /Analytics/video/{videoid}
GET /Analytics/channel/{channelid}


# High Level Design

## Video Uploading 

The video itself can be stored on the S3 bucket, this can be done directly by the user with help of a presigned url
in similar way the cient can implement chunking.

We need different databases, to store this information about the metadata + chunk information ( versions, timestamp, storageLink)
while the metadata can be wide column database like cassandra, where id could be userid_timestampid_channelid
this metadata information can fed through a changeData Capture into the something as elasticSearch. Which can help with the search operation performed based on tags / keywords / titles / User / Channel Name

metadata can also be cached for quick retrieval of hot / popular videos

chunk information can be a simple SQL DB like mysql to store the videoId, version, timestamp, chunkId, storageLink
we can index this information as well in CDN

this chunk information can be fed into a change data capture /KKafka queue to be processed to converted this chunks to different bitrates/ resolutions.
we can have manifest table as well for this, to store the manifest file for the videoID once this processing is finished.

this chunking can directly be handled after the video is uploaded to S3, and the video processor and do the transcoding / compression / conversion / audio processing / DMRC checks

the chunking also helps us with resumable uploads

## Video Streaming
this is the biggest requirement, to server the users with quick access to streaming information about the video and serve it across various bitrates and various resolutions

the above way to handle the upload, automatically the streaming / adaptive bitrates, the client can monitor the drops in the internet connection and request a different resolutions packet if available in the manifest files
due to the chunking information, we know the next chunks or group of chunks we want to request.

