# Kodi path rules

## Concepts
### Kodi collection
The Kodi collection is the path holding the information about media type and scrapper.  
This path does not have to be matching a jellyfin library.  

## Movies
You can have one collection or more.  
Movies can be directly linked to the path of the collection, or in a sub path.  
Kodi is not bothered by path rules, meaning the following structure is ok:
| strPath | strContent | strScraper | idParentPath | has movie files |
| --- | --- | --- | --- | --- |
| plugin://plugin.video.jellyfin/{libraryId1} | movies | metadata.local | NULL | Yes |
| plugin://plugin.video.jellyfin/{libraryId2} | movies | metadata.local | NULL | Yes |

## TV shows
You can have one collection or more.  
Kodi expects to have the following folder structure:  
```Collection/tvShow/episode```  
Without this type of structure, context menus will not work properly.  
Also, Kodi checks that the folder *tvShow* of the previous structure is in fact a child of *Collection*, meaning the full *Collection* path is in the path of *tvShow*.  

The right way to do it should be  
| idPath | strPath | strContent | strScraper | idParentPath | has episode files |
| --- | --- | --- | --- | --- | --- |
| 1 | plugin://plugin.video.jellyfin/{libraryId1} | tvshows | metadata.local | NULL | No |
| 2 | plugin://plugin.video.jellyfin/{libraryId1}/{tvShowId1} | NULL | NULL | 1 | No |
| 3 | plugin://plugin.video.jellyfin/{libraryId1}/{tvShowId1}/{seasonId1} | NULL | NULL | 2 | YES |
| 4 | plugin://plugin.video.jellyfin/{libraryId2} | tvshows | metadata.local | NULL | No |
| 5 | plugin://plugin.video.jellyfin/{libraryId2}/{tvShowId2} | NULL | NULL | 4 | No |
| 6 | plugin://plugin.video.jellyfin/{libraryId2}/{tvShowId2}/{seasonId2} | NULL | NULL | 5 | YES |
| 7 | plugin://plugin.video.jellyfin/{libraryId2}/{tvShowId2}/{seasonId3} | NULL | NULL | 5 | YES |
| 8 | plugin://plugin.video.jellyfin/{libraryId2}/{tvShowId3} | NULL | NULL | 4 | No |
| 9 | plugin://plugin.video.jellyfin/{libraryId2}/{tvShowId3}/{seasonId4} | NULL | NULL | 8 | YES |
| 10 | plugin://plugin.video.jellyfin/{libraryId2}/{tvShowId3}/{seasonId5} | NULL | NULL | 8 | YES |
  
Then we have to fix what may be broken in the plugin by the changes of these links.  
It should be only in the Files table fileName generation.