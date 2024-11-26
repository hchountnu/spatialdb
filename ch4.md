# 地理数据库 範例 
 
**朱选, 刘素霞 (2006)地理信息系统原理与技术 华东师范大学出版社**

以下以SQL作為操作語言。
- QGIS中應用資料庫，參見[QGIS Browser panel](https://docs.qgis.org/3.34/en/docs/user_manual/introduction/browser.html) 及 [Using Spatial Databases in QGIS](https://docs.qgis.org/3.34/en/docs/training_manual/databases/index.html)。QGIS 可透過 [DB Manager Plugin](https://docs.qgis.org/3.34/en/docs/user_manual/plugins/core_plugins/plugins_db_manager.html)使用SQL。
- ArcGIS Pro 中應用資料庫系統參見 [Databases and ArcGIS](https://pro.arcgis.com/en/pro-app/latest/help/data/databases/databases-and-arcgis.htm) ArcGIS 不直接支援SQL。



## 建立 ***住宅***、***住戶*** 、***戶主*** 表格 

```SQL
-- Table: 住宅
CREATE TABLE 住宅 (
       住宅編號 INT,
       街道名稱 CHAR(30),
       郵政編碼 INT,
       住宅類型 CHAR(20),
       戶數     SMALLINT
);
INSERT INTO 住宅 (住宅編號, 街道名稱, 郵政編碼, 住宅類型, 戶數) VALUES (10001, '鼓樓北路', 226300, '低層樓房', 36);
INSERT INTO 住宅 (住宅編號, 街道名稱, 郵政編碼, 住宅類型, 戶數) VALUES (10002, '鼓樓北路', 226300, '低層樓房', 36);
INSERT INTO 住宅 (住宅編號, 街道名稱, 郵政編碼, 住宅類型, 戶數) VALUES (10003, '鼓樓北路', 226300, '高層樓房', 48);
INSERT INTO 住宅 (住宅編號, 街道名稱, 郵政編碼, 住宅類型, 戶數) VALUES (10004, '鼓樓北路', 226300, '高層樓房', 48);
INSERT INTO 住宅 (住宅編號, 街道名稱, 郵政編碼, 住宅類型, 戶數) VALUES (20001, '府后路', 226400, '平房', 5);
INSERT INTO 住宅 (住宅編號, 街道名稱, 郵政編碼, 住宅類型, 戶數) VALUES (20002, '金陵路', 226500, '高層樓房', 54);
INSERT INTO 住宅 (住宅編號, 街道名稱, 郵政編碼, 住宅類型, 戶數) VALUES (11003, '思泉巷', 226600, '平房', 4);

-- Table: 住戶
CREATE TABLE 住戶 (
       住戶編號 INT,
       戶主編號 INT,
       家庭人口 SMALLINT,
       住宅編號 INT,
       住址     CHAR(30)
);
INSERT INTO 住戶 (住戶編號, 戶主編號, 家庭人口, 住宅編號, 住址) VALUES (210001, 180033, 4, 20002, '金陵路120號');
INSERT INTO 住戶 (住戶編號, 戶主編號, 家庭人口, 住宅編號, 住址) VALUES (210002, 102004, 3, 10004, '鐘鼓北路45號');
INSERT INTO 住戶 (住戶編號, 戶主編號, 家庭人口, 住宅編號, 住址) VALUES (210003, 102006, 4, 10002, '鐘鼓北路43號');
INSERT INTO 住戶 (住戶編號, 戶主編號, 家庭人口, 住宅編號, 住址) VALUES (210004, 180031, 5, 20001, '府后路15號');
INSERT INTO 住戶 (住戶編號, 戶主編號, 家庭人口, 住宅編號, 住址) VALUES (310001, 180032, 4, 10001, '鐘鼓北路41號');
INSERT INTO 住戶 (住戶編號, 戶主編號, 家庭人口, 住宅編號, 住址) VALUES (310002, 103000, 2, 11003, '思泉巷5號');

-- Table: 戶主
CREATE TABLE 戶主 (
      戶主編號 INT,
	  姓名 CHAR(10),
	  性別 CHAR(2),
	  年齡 SMALLINT,
	  職業 CHAR(30)
);
INSERT INTO 戶主 (戶主編號, 姓名, 性別, 年齡, 職業) VALUES (200001, '朱長生', '男', 35, '大學講師');
INSERT INTO 戶主 (戶主編號, 姓名, 性別, 年齡, 職業) VALUES (102004, '李永其', '男', 41, '工程師');
INSERT INTO 戶主 (戶主編號, 姓名, 性別, 年齡, 職業) VALUES (102005, '王玲', '女', 40, '工人');
INSERT INTO 戶主 (戶主編號, 姓名, 性別, 年齡, 職業) VALUES (102006, '周小國', '男', 36, '工人');
INSERT INTO 戶主 (戶主編號, 姓名, 性別, 年齡, 職業) VALUES (180031, '張文靜', '女', 44, '教師');
INSERT INTO 戶主 (戶主編號, 姓名, 性別, 年齡, 職業) VALUES (180032, '李光耀', '男', 58, '經理');
INSERT INTO 戶主 (戶主編號, 姓名, 性別, 年齡, 職業) VALUES (180033, '周進', '男', 65, '教師');
INSERT INTO 戶主 (戶主編號, 姓名, 性別, 年齡, 職業) VALUES (103000, '張三', '男', 35, '經理');

```
## 查詢 ***住宅***、***住戶*** 、***戶主*** 表格

```SQL
SELECT 住宅編號,街道名稱,住宅類型 FROM 住宅;

SELECT * FROM 住宅 WHERE 住宅類型 = '高層樓房';

SELECT 姓名,性別,年齡,職業 
FROM 戶主 
WHERE 年齡 BETWEEN 40 AND 50 
ORDER BY 年齡 ASC;

SELECT 住戶.戶主編號,家庭人口,住址,姓名,年齡,職業
FROM  住戶,戶主
WHERE 住戶.戶主編號=戶主.戶主編號;

SELECT 住戶.戶主編號,家庭人口,住址,姓名,年齡,職業
FROM  住戶 INNER JOIN 戶主 ON 住戶.戶主編號=戶主.戶主編號;

SELECT 住戶.戶主編號,家庭人口,住址,姓名,年齡,職業
FROM  住戶 LEFT OUTER JOIN 戶主 ON 住戶.戶主編號=戶主.戶主編號;

SELECT 住戶.戶主編號,家庭人口,住址,姓名,年齡,職業
FROM  住戶 CROSS JOIN 戶主 ON 住戶.戶主編號=戶主.戶主編號;

SELECT 住戶.戶主編號,家庭人口,住址,姓名,年齡,職業
FROM  住戶 CROSS JOIN 戶主;

SELECT 住戶.戶主編號,家庭人口,住址,姓名,年齡,職業
FROM  住戶,戶主;

SELECT *
FROM  住戶,戶主;

SELECT 住戶.戶主編號,家庭人口,住址,姓名,年齡,職業
FROM  住戶 CROSS JOIN 戶主
WHERE 住戶.戶主編號=戶主.戶主編號;

SELECT 街道名稱,SUM(戶數) as 總戶數
FROM 住宅
GROUP BY 街道名稱;

SELECT 姓名,性別,年齡,職業
FROM  戶主
WHERE EXISTS (SELECT * FROM 住戶 WHERE 戶主.戶主編號 = 戶主編號 AND 家庭人口 > 3);

SELECT 姓名,性別,年齡,職業
FROM  戶主
WHERE 戶主編號 IN (SELECT 戶主編號 FROM 住戶 WHERE 戶主.戶主編號 = 戶主編號 AND 家庭人口 > 3);
```

## 建立 ***住宅***、***住戶*** 、***戶主*** 表格 (有 constraint)

```SQL

-- Table: 戶主
CREATE TABLE 戶主 (
      戶主編號 INT,
	  姓名 CHAR(10),
	  性別 CHAR(2),
	  年齡 SMALLINT CHECK (年齡 >= 0 AND 年齡 < 200),
	  職業 CHAR(30),
	  CONSTRAINT  戶主_pk PRIMARY KEY (戶主編號));
	 
INSERT INTO 戶主 (戶主編號, 姓名, 性別, 年齡, 職業) VALUES (200001, '朱長生', '男', 35, '大學講師');
INSERT INTO 戶主 (戶主編號, 姓名, 性別, 年齡, 職業) VALUES (102004, '李永其', '男', 41, '工程師');
INSERT INTO 戶主 (戶主編號, 姓名, 性別, 年齡, 職業) VALUES (102005, '王玲', '女', 40, '工人');
INSERT INTO 戶主 (戶主編號, 姓名, 性別, 年齡, 職業) VALUES (102006, '周小國', '男', 36, '工人');
INSERT INTO 戶主 (戶主編號, 姓名, 性別, 年齡, 職業) VALUES (180031, '張文靜', '女', 44, '教師');
INSERT INTO 戶主 (戶主編號, 姓名, 性別, 年齡, 職業) VALUES (180032, '李光耀', '男', 58, '經理');
INSERT INTO 戶主 (戶主編號, 姓名, 性別, 年齡, 職業) VALUES (180033, '周進', '男', 65, '教師');
INSERT INTO 戶主 (戶主編號, 姓名, 性別, 年齡, 職業) VALUES (103000, '張三', '男', 35, '經理');



-- Table: 住宅
CREATE TABLE 住宅 (
       住宅編號 INT,
       街道名稱 CHAR(30),
       郵政編碼 INT,
       住宅類型 CHAR(20) CHECK (住宅類型 in ('低層樓房','高層樓房','平房')),
       戶數     SMALLINT,
       CONSTRAINT  住宅_pk PRIMARY KEY (住宅編號));
      
INSERT INTO 住宅 (住宅編號, 街道名稱, 郵政編碼, 住宅類型, 戶數) VALUES (10001, '鼓樓北路', 226300, '低層樓房', 36);
INSERT INTO 住宅 (住宅編號, 街道名稱, 郵政編碼, 住宅類型, 戶數) VALUES (10002, '鼓樓北路', 226300, '低層樓房', 36);
INSERT INTO 住宅 (住宅編號, 街道名稱, 郵政編碼, 住宅類型, 戶數) VALUES (10003, '鼓樓北路', 226300, '高層樓房', 48);
INSERT INTO 住宅 (住宅編號, 街道名稱, 郵政編碼, 住宅類型, 戶數) VALUES (10004, '鼓樓北路', 226300, '高層樓房', 48);
INSERT INTO 住宅 (住宅編號, 街道名稱, 郵政編碼, 住宅類型, 戶數) VALUES (20001, '府后路', 226400, '平房', 5);
INSERT INTO 住宅 (住宅編號, 街道名稱, 郵政編碼, 住宅類型, 戶數) VALUES (20002, '金陵路', 226500, '高層樓房', 54);
INSERT INTO 住宅 (住宅編號, 街道名稱, 郵政編碼, 住宅類型, 戶數) VALUES (11003, '思泉巷', 226600, '平房', 4);

-- Table: 住戶
CREATE TABLE 住戶 (
       住戶編號 INT,
       戶主編號 INT,
       家庭人口 SMALLINT,
       住宅編號 INT,
       住址     CHAR(30),
       CONSTRAINT  住戶_pk PRIMARY KEY (住戶編號),
       CONSTRAINT  fk_住戶_戶主 FOREIGN KEY (戶主編號) REFERENCES 戶主(戶主編號),
       CONSTRAINT  fk_住戶_住宅 FOREIGN KEY (住宅編號) REFERENCES 住宅(住宅編號));
      
INSERT INTO 住戶 (住戶編號, 戶主編號, 家庭人口, 住宅編號, 住址) VALUES (210001, 180033, 4, 20002, '金陵路120號');
INSERT INTO 住戶 (住戶編號, 戶主編號, 家庭人口, 住宅編號, 住址) VALUES (210002, 102004, 3, 10004, '鐘鼓北路45號');
INSERT INTO 住戶 (住戶編號, 戶主編號, 家庭人口, 住宅編號, 住址) VALUES (210003, 102006, 4, 10002, '鐘鼓北路43號');
INSERT INTO 住戶 (住戶編號, 戶主編號, 家庭人口, 住宅編號, 住址) VALUES (210004, 180031, 5, 20001, '府后路15號');
INSERT INTO 住戶 (住戶編號, 戶主編號, 家庭人口, 住宅編號, 住址) VALUES (310001, 180032, 4, 10001, '鐘鼓北路41號');
INSERT INTO 住戶 (住戶編號, 戶主編號, 家庭人口, 住宅編號, 住址) VALUES (310002, 103000, 2, 11003, '思泉巷5號');

```

# 建立 ***學校***、***住宅樓***  PostGIS 空間表格

## 建立資料庫

可在同一Postgres的任一資料庫中執行，需要管理權限  

```SQL
CREATE DATABASE ch4_spatial;
```
database 啟用 postgis extension 僅需執行一次,需要管理權限  
PostgresSQL database 有schema, 以下將在 public schema 中建立 

```SQL
CREATE EXTENSION postgis;
```

為了避免PostGIS extension的檔案，和其他檔案混淆，常將PostGIS安裝在獨立的schema中,並設定權限與執行的路徑

```SQL
CREATE SCHEMA postgis;
GRANT USAGE ON schema postgis to public;
CREATE EXTENSION postgis SCHEMA postgis;
ALTER DATABASE my_postgis_db SET search_path=public,postgis,contrib;
```

查詢 postgis extension 版本
```SQL
SELECT PostGIS_Full_Version();
```

定義表格，具座標系統，如不指明座標，則預設為 EPGS4326

```SQL
CREATE TABLE 學校(
	名稱 VARCHAR(40) PRIMARY KEY,
	地址 VARCHAR(100),
	教師人數 INT,
	學生人數 INT,
	幾何形體 geometry(POINT,32651));
	
CREATE TABLE 住宅樓(
	樓號 INT,
	地址 VARCHAR(100),
	幾何形體 geometry(POINT,32651),
	PRIMARY KEY (樓號,地址));
```

第二種加入幾何欄位方法，先建立沒有幾何欄位表格，然後以	AddGeometryColumn() 加入幾何欄位，和 Spatialite相似。  


```SQL	
CREATE TABLE 學校(
	名稱 VARCHAR(40)  PRIMARY KEY,
	地址 VARCHAR(100),
	教師人數 INT,
	學生人數 INT);
	
CREATE TABLE 住宅樓(
	樓號 INT,
	地址 VARCHAR(100),
	PRIMARY KEY (樓號,地址));
	
SELECT AddGeometryColumn('學校', '幾何形體', 32651, 'POINT', 2);
SELECT AddGeometryColumn('住宅樓', '幾何形體', 32651, 'POINT', 2);	

```

第三種加入幾何欄位方法，是使用 ALTER TABLE 加入。適用於在既有的表格中，增加空間資訊

```SQL	
CREATE TABLE 學校(
	名稱 VARCHAR(40)  PRIMARY KEY,
	地址 VARCHAR(100),
	教師人數 INT,
	學生人數 INT);
	
CREATE TABLE 住宅樓(
	樓號 INT,
	地址 VARCHAR(100),
	PRIMARY KEY (樓號,地址));

ALTER TABLE 學校 ADD COLUMN 幾何形體 geometry(Point,32651);
ALTER TABLE 住宅樓 ADD COLUMN 幾何形體 geometry(Point,32651);
```


```SQL	
-- 指定座標，則加入資料時，必須為table定義時，相同的座標。	
INSERT INTO "學校" VALUES ('華英中學','東方路10號',200,1800,ST_GeomFromText('POINT(341845 3431829)',32651)),
                         ('東風小學','鼓樓路350號',150,1000,ST_GeomFromText('POINT(339000 3432300)',32651)),
                         ('南洋中學','烏吉路1010號',100,1200,ST_GeomFromText('POINT(343000 3430200)',32651));

INSERT INTO "住宅樓" VALUES (103,'南洋大道101C',ST_GeomFromText('POINT(341816 3431000)',32651)),
                           (205,'上海路30號',ST_GeomFromText('POINT(342000 3430500)',32651)),
                           (715,'湖南54號',ST_GeomFromText('POINT(340000 3431500)',32651));
```

## 建立 ***學校***、***住宅樓***  Spatialite 空間表格

### Sqlite 的編碼
- sqlite 及 spatialite 資料庫預設編碼為 UTF-8  
- 中文 window 作業系統 Windows 10 1909 之前的版本，預設為 BIG5編碼。和許多軟體預設編碼不同，因此容易造成衝突。
- Spatialite/Sqlite 的 shell 版本，window命令視窗 受作業系統編碼影響。
- windows10 1909以後及Window 11,雖然系統預設為UTF-8,但Sqlite 仍不能使用中文,需改為英文
- Spatialite 中文輸入時，有時會亂碼。需使用 " " quote 名稱。
- spatialite-gui 及 DB Browser for Sqlite, Dbeaver 等client 以 UTF-8 為預設編碼。所以可以輸入UTF-8編碼。

```SQL
-- sqlite,spatilite 設定使用內碼
PRAGMA encoding = "UTF-8";
-- 詢資料庫內碼
PRAGMA encoding;
```
### 載入 Spatiate plugin
 Dbeaver 為 java 開發，無法使用winsows 的dll plugin mod_spatialite.dll   
 Sqlite 使用 .command 載入 mod_spatialite.dll

```SQL
.load mod_spatialite
```
 或使用 SQL 指令
```SQL
SELECT load_extension('mod_spatialite.dll'); 
```
#### 啟用 spatialite plugin 

載入後，再啟用spatialite plugin，將 產生 spatial database 的結構 
-- http://www.gaia-gis.it/gaia-sins/spatialite-cookbook/html/metadata.html  
spatialite 及 spatialite_gui 建立新資料庫時，都會自動執行任何所需的初始化任務。所以不需要明確呼叫。
QGIS或ArcGIS 也會自動進行必要初始化。

``` SQL
spatialite ch4.spatialite
```
```SQL
--  sqlite client 載入plugin 後, 需手動啟動。
SELECT InitSpatialMetaData(); -- sqlite 非常慢

-- 查詢 spatialite 啟動後，產生哪些table
.tables

-- DB Browser for Sqlite 可載入 mod_spatialite.dll plugin, 但無法正確建立空間資料表結構。
```

- QGIS  使用 SpatiaLite 參見 [Working with SpatiaLite databases in QGIS](https://docs.qgis.org/3.34/en/docs/training_manual/databases/spatialite.html) 

- ArcGIS Pro 製作Spatialite 資料庫，參見 [Create SQLite Database](https://pro.arcgis.com/en/pro-app/latest/tool-reference/data-management/create-sqlite-database.htm)

### 定義表格
```SQL
-- spatialite and postgis 定義表格，先定義無空間屬性表個，然後加上空間表格
-- 先定義沒有空間 geometry的表格
CREATE TABLE IF NOT EXISTS "學校" (
	"名稱"	VARCHAR(40),
	"地址"	VARCHAR(100),
	"教師人數"	INT,
	"學生人數"	INT
);


CREATE TABLE IF NOT EXISTS "住宅樓" (
	"樓號"	INT,
	"地址"	VARCHAR(100)
);

-- 使用 AddGeometryColumn() 建立空間表格。
-- https://postgis.net/docs/AddGeometryColumn.html
-- 
-- 建立geomtray 欄位時需設定投影
-- SRID of 0 表示沒有 no SRID 為預設值 
-- 課本並無坐標系統
-- 街道名推測是 江蘇省泰州市
-- 本該用 EPSG2364，但和書中範圍一致，尤其Y坐標僅有5位，應為區域坐標
-- 本例假設為投影 EPSG 32651  WGS 84/UTM zone 51N   https://epsg.io/32651
-- 但課本原始資料中的坐標將會在赤道附近，需調整 x+320000       y+3360000
SELECT AddGeometryColumn('學校', '幾何形體', 32651, 'POINT', 'XY');
SELECT AddGeometryColumn('住宅樓', '幾何形體', 32651, 'POINT', 'XY');

INSERT INTO "學校" VALUES ('華英中學','東方路10號',200,1800,ST_GeomFromText('POINT(341845 3431829)',32651)),
                         ('東風小學','鼓樓路350號',150,1000,ST_GeomFromText('POINT(339000 3432300)',32651)),
                         ('南洋中學','烏吉路1010號',100,1200,ST_GeomFromText('POINT(343000 3430200)',32651));

INSERT INTO "住宅樓" VALUES (103,'南洋大道101C',ST_GeomFromText('POINT(341816 3431000)',32651)),
                           (205,'上海路30號',ST_GeomFromText('POINT(342000 3430500)',32651)),
                           (715,'湖南54號',ST_GeomFromText('POINT(340000 3431500)',32651));

```


## 建立DuckDB 空間資料

[DuckDB](https://duckdb.org/) 
- [DuckDB Spatial 說明文件]
  - [DuckDB Spatial Extension 說明文件](https://duckdb.org/docs/extensions/spatial/overview.html)
- [GDAL Vector Drivers 說明文件](https://gdal.org/en/latest/drivers/vector/index.html#vector-drivers)

資料來源：[GitHub - hchountnu/spatialdb](https://github.com/hchountnu/spatialdb)

```SQL

-- 查詢DuckDB extension
SELECT *
FROM duckdb_extensions();

-- 啟動DuckDB spatial extension
INSTALL spatial; -- 僅需執行一次
LOAD spatial;

-- 測試空間功能
SELECT ST_POINT(121.52662762238074,25.024985016796563) 教室位置;

-- 建立表格
CREATE TABLE 學校(
	名稱 VARCHAR(40) PRIMARY KEY,
	地址 VARCHAR(100),
	教師人數 INT,
	學生人數 INT,
	幾何形體 geometry);
	
CREATE TABLE 住宅樓(
	樓號 INT,
	地址 VARCHAR(100),
	幾何形體 geometry,
	PRIMARY KEY (樓號,地址));

-- 插入資料
INSERT INTO "學校" VALUES ('華英中學','東方路10號',200,1800,ST_POINT(341845,3431829)),
                         ('東風小學','鼓樓路350號',150,1000,ST_POINT(339000,3432300)),
                         ('南洋中學','烏吉路1010號',100,1200,ST_POINT(343000,3430200));

INSERT INTO "住宅樓" VALUES (103,'南洋大道101C',ST_POINT(341816,3431000)),
                           (205,'上海路30號',ST_POINT(342000,3430500)),
                           (715,'湖南54號',ST_POINT(340000,3431500));

```
### 使用csv 資料

- 學校.CSV
```csv
"名稱","地址","教師人數","學生人數","long","lat","geom"
華英中學,東方路10號,200,1800,121.3432834387615,31.00946430365703,POINT(121.3432834387615 31.00946430365703)
東風小學,鼓樓路350號,150,1000,121.31341729800188,31.01332659699219,POINT(121.31341729800188 31.01332659699219)
南洋中學,烏吉路1010號,100,1200,121.35563037769106,30.994926211784282,POINT(121.35563037769106 30.994926211784282)
```
- 住宅樓.CSV
```
"樓號","地址","long","lat","geom"
103,南洋大道101C,121.34310911481839,31.00198329777303,POINT(121.34310911481839 31.00198329777303)
205,上海路30號,121.34511363627745,30.997498285331968,POINT(121.34511363627745 30.997498285331968)
715,湖南54號,121.32401544764056,31.00624753599756,POINT(121.32401544764056 31.00624753599756)
```


```SQL
-- 硬碟讀取
SELECT * FROM 'D:/class/spatiaDB/關連式資料庫範例（朱選）/學校.csv';
-- 網址讀取
SELECT * FROM 'https://raw.githubusercontent.com/hchountnu/spatialdb/refs/heads/main/學校.csv';

-- 由 CSV 產生新表格
CREATE TABLE 學校 AS
SELECT * FROM 'E:/research/授課/地理資訊/素材/關連式資料庫範例（朱選）/學校.csv'

-- 產生 point geometry 的方法
SELECT 名稱,地址,教師人數,學生人數, ST_POINT(long,lat) 
FROM 學校;

SELECT 名稱,地址,教師人數,學生人數, ST_GeomFromText(geom) 
FROM 學校;

SELECT 名稱,地址,教師人數,學生人數, ST_GeomFromText( 'POINT('||long||' '||lat||')') 
FROM 學校;

-- 增加 geometry field
ALTER TABLE 學校 ADD COLUMN 幾何形體 GEOMETRY;

UPDATE 學校 SET 幾何形體 = st_point(long,lat)
WHERE TRUE;

-- 將 EPGS 4326 WGS84 經緯度，轉換 EPSG 32651 UTM 座標，
-- 必須注意 WGS84的原始定義中，採用 (lat long), 在st_transform 中，必須說明輸入為 (long,lat)格式。因此最後的 TURE 指是 always_xy 為 TRUE。參見 https://duckdb.org/docs/extensions/spatial/functions#st_transform
UPDATE 學校 SET 幾何形體 = st_transform(st_point(long,lat),'EPSG:4326','EPSG:32651',TRUE)
WHERE TRUE;

-- 使用 GDAL 讀入空間資料
-- 查詢支援的 GDAL 驅動程式和檔案格式的清單
SELECT  *
FROM ST_Drivers();

-- ST_READ() 讀入空間資料
-- 根據檔名，自動選取適當讀入方法。此例自動選擇讀取shappe 格式。 
-- Duckdb 沒有包含 iconv，僅支援 UTF-8 encoding
SELECT  *
FROM 'E:/research/授課/地理資訊/素材/關連式資料庫範例（朱選）/住宅樓.shp';
SELECT  *
FROM 'E:/research/授課/地理資訊/素材/關連式資料庫範例（朱選）/學校.shp';


-- OR 指定用 ST_READ() 讀取空間資料，副檔名.shp, 則自動選擇shape格式。
SELECT * 
FROM ST_READ('E:/research/授課/地理資訊/素材/關連式資料庫範例（朱選）/住宅樓.shp');

SELECT * 
FROM ST_READ('E:/research/授課/地理資訊/素材/關連式資料庫範例（朱選）/學校.shp');

-- 將shp等相關檔壓縮成zip後，成shp.zip 後讀取

-- 住宅樓的 URL encode為 %E4%BD%8F%E5%AE%85%E6%A8%93
SELECT *
FROM ST_READ('https://github.com/hchountnu/spatialdb/raw/refs/heads/main/%E4%BD%8F%E5%AE%85%E6%A8%93.shp.zip');


-- 學校.shp.zip 的URL encode為 %E5%AD%B8%E6%A0%A1
SELECT *
FROM ST_READ('https://github.com/hchountnu/spatialdb/raw/refs/heads/main/%E5%AD%B8%E6%A0%A1.shp.zip');


-- OR 用 ST_READ() 讀取空間資料，副檔名.gpkg, 則自動選擇GeoPackage格式。
SELECT *
FROM ST_READ('E:/research/授課/地理資訊/素材/關連式資料庫範例（朱選）/ch4.gpkg',layer='住宅樓');

SELECT *
FROM ST_READ('E:/research/授課/地理資訊/素材/關連式資料庫範例（朱選）/ch4.gpkg',layer='學校');

SELECT *
FROM ST_READ('https://github.com/hchountnu/spatialdb/raw/refs/heads/main/ch4.gpkg',layer='住宅樓');

SELECT *
FROM ST_READ('https://github.com/hchountnu/spatialdb/raw/refs/heads/main/ch4.gpkg',layer='學校');

-- 利用讀入資料，產生新TABLE
CREATE TABLE 住宅樓 AS
SELECT * 
FROM ST_READ('E:/research/授課/地理資訊/素材/關連式資料庫範例（朱選）/ch4.gpkg',layer='住宅樓');

CREATE TABLE 學校 AS
SELECT * 
FROM ST_READ('E:/research/授課/地理資訊/素材/關連式資料庫範例（朱選）/ch4.gpkg',layer='學校');


```


# spatial query

spatialite 除了可查詢本身的空間資料外，也可使用 geopackage 格式的空間資料，但需先起啟用 EnableGpkgAmphibiousMode()、才能讀取空間資料。

- QGIS 使用 Browser panel

```SQL
-- for Spatialite
select EnableGpkgAmphibiousMode();
```
```SQL

SELECT Find_SRID('public','住宅樓','幾何形體'); --PostGIS only
SELECT ST_Srid(幾何形體) FROM 住宅樓;

--  學校和住宅距離
SELECT 住宅樓.樓號, 住宅樓.地址 AS 住宅地址, 
       學校.名稱 AS 學校名稱, 學校.地址 AS 學校地址,
	   ST_DISTANCE(住宅樓.幾何形體,學校.幾何形體)  AS 距離
FROM  住宅樓
CROSS JOIN  學校
ORDER BY 住宅樓.樓號, 距離;

-- 住宅到學校距離
SELECT 住宅樓.樓號, 住宅樓.地址 AS 住宅地址, 
       學校.名稱 AS 學校名稱, 學校.地址 AS 學校地址,
	   ST_DISTANCE(住宅樓.幾何形體,學校.幾何形體) AS 距離
FROM 住宅樓, 學校
WHERE 住宅樓.地址 = '南洋大道101C' AND
      學校.名稱 = '華英中學';

-- 住宅到學校距離,並輸出為連線幾何物件
SELECT 住宅樓.樓號, 住宅樓.地址 AS 住宅地址, 
       學校.名稱 AS 學校名稱, 學校.地址 AS 學校地址, 
	   ST_DISTANCE(住宅樓.幾何形體,學校.幾何形體) AS 距離,
	 --St_MakeLine(住宅樓.幾何形體,學校.幾何形體 )As line -- for PostGIS, duckdb
	 --MakeLine(住宅樓.幾何形體,學校.幾何形體 )As line    -- for Spatialite
FROM 住宅樓
CROSS JOIN  學校
ORDER BY 住宅樓.樓號, 距離; 

-- 找到每個住宅樓最近的學
-- SQL WINDOW function 可參考 https://haosquare.com/sql-window-function-intro/
SELECT * 
FROM (
     SELECT 樓號, 住宅地址, 學校名稱, 學校地址,	距離,
	        RANK() OVER (PARTITION BY 樓號, 住宅地址 ORDER BY 距離) as 遠近  -- SQL WINDOW function
     FROM 
         (
		  SELECT 住宅樓.樓號 as 樓號, 住宅樓.地址 AS 住宅地址, 
                 學校.名稱 AS 學校名稱, 學校.地址 AS 學校地址,
	             ST_DISTANCE(住宅樓.幾何形體,學校.幾何形體)  AS 距離
          FROM  住宅樓
          CROSS JOIN  學校)  AS r1
	  )  AS r2 
WHERE 遠近 = 1; 

-- 列出坐標位置 spatialite
SELECT 樓號, 地址, ASTEXT(幾何形體) AS 位置
FROM 住宅樓;

-- 列出坐標位置 postgis , spatialite
SELECT 名稱, 地址, ST_ASTEXT(幾何形體) AS 位置
FROM 學校;

-- 轉換為WGS84,並列出經度及緯度，以便於轉成CSV檔案
select 名稱, 地址, 教師人數, 學生人數, st_x(st_transform(幾何形體, 4326)) as long, st_y(st_transform(幾何形體, 4326)) as lat
from 學校;

-- BUFFER 產生學區
SELECT "名稱", "地址", ST_BUFFER(幾何形體,1000) AS "學區"
FROM "學校";

SELECT "名稱", "地址", ST_ASTEXT(ST_BUFFER(幾何形體,1000)) AS "學區"
FROM "學校";

-- 學區內住宅
SELECT 學校.名稱 AS 學校名稱,住宅樓.地址 AS 住宅地址
FROM 學校,住宅樓
WHERE ST_Within(住宅樓.幾何形體,ST_BUFFER(學校.幾何形體,1500));
-- OR
SELECT "學校"."名稱" AS "學校名稱","住宅樓"."地址" AS "住宅地址"
FROM "學校" 
CROSS JOIN  "住宅樓"
WHERE ST_Within(住宅樓.幾何形體,ST_BUFFER(學校.幾何形體,1500));
-- OR
SELECT 學校.名稱 AS 學校名稱,住宅樓.地址 AS 住宅地址
FROM 學校
JOIN 住宅樓
ON ST_Within(住宅樓.幾何形體,ST_BUFFER(學校.幾何形體,1500));

```
