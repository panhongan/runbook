**[Tools]** 
**1. dump-segment**  
1). config json  
```
{
  "type": "compact",
  "dataSource": "experience_insights.timelines.PT1M.1",
  "segmentGranularity": "HOUR",
  "dimensionsSpec": {
    "dimensions": [
      "customerId",
      "clientId",
      "sessionId",
      "sessionStartTimeMs",
      "version",
      {
        "type": "string",
        "name": "videoSessionJson",
        "createBitmapIndex": false
      }
    ],
    "dimensionExclusions": []
  },
  "ioConfig": {
    "inputSpec": {
      "type": "interval",
      "interval": "2021-03-25T00:00:00Z/2021-03-25T01:00:00Z"
    }
  },
  "tuningConfig": {
    "type": "index_parallel",
    "partitionsSpec": {
      "type": "hashed",
      "targetRowsPerSegment": 2500000,
      "partitionDimensions": [
        "clientId",
        "sessionId"
      ]
    },
    "forceGuaranteedRollup": true,
    "maxNumConcurrentSubTasks": 20,
    "indexSpec": {
      "bitmap": {
        "type": "roaring",
        "compressRunOnSerialization": true
      },
      "dimensionCompression": "lz4",
      "metricCompression": "lz4",
      "longEncoding": "longs",
      "segmentLoader": null
    },
    "indexSpecForIntermediatePersists": {
      "bitmap": {
        "type": "roaring",
        "compressRunOnSerialization": true
      },
      "dimensionCompression": "lz4",
      "metricCompression": "lz4",
      "longEncoding": "longs",
      "segmentLoader": null
    }
  }
}
```
<br></br>

2). run dump-segment class  
```
java -server -XX:+IgnoreUnrecognizedVMOptions \
    -XX:+UseG1GC -Xms2g -Xmx2g \
    -cp <druid_home>>/lib/*: \
    org.apache.druid.cli.Main tools dump-segment \
    --directory /mnt/dump-segment/unzip/ \
    --out /mnt/dump-segment/dump/segment.txt

--directory: the segment directory or the target directory after index file unzipped
```
