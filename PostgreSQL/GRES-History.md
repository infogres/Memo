### GRESファミリー(INGRES/POSTGRES関連RDBMS)の系譜

<!-- 
object "INFORMIX-Turbo 4" as Informix4 {
Informix Corporation, 1989
} 
-->


@startuml
object "Edgar F. Codd" as Ted {
"A Relational Model of Data for Large Shared Data Banks"
(1970), IBM San Jose Research Laboratory

}


object "Michael Stonebreaker" as Mike {
U.C. Berkeley
}
object "University INGRES" as uIngres {
  U.C. Berkeley
}
object "University INGRES 8.9" as uIngres8 {
  U.C. Berkeley
}
object "RTI Ingres" as rtIngres {
Relationa Technology Inc.
}
object "CA Ingres r3" as caIngres3 {
Computer Assosiates
}
object "CA Ingres 2006" as caIngres2006 {
Computer Assosiates
}
object "IC Ingres" as icIngres {
Ingres Corporation
}
object "Actian Ingres 10" as aIngres10 {
Actian Corporation
}
object "Actian X Ingres 11" as aIngres11 {
Actian Corporation
}

Ted <|-- Mike : 1974
Mike <|.. uIngres : 1974
Mike <|.. rtIngres
uIngres --> rtIngres : 1980
uIngres --> uIngres8 : 1985
rtIngres ---> caIngres3 : 2004
caIngres3 --> caIngres2006 : 2006
caIngres2006 --> icIngres
icIngres ---> aIngres10 : 2011
aIngres10 ---> aIngres11 : 2017




object "Sybase" as Sybase {
Sybase Inc.
}
object "SQL Server 4.21" as SQLServer {
Sybase, Microsoft, Ashton-Tate
}
object "Microsoft SQL Server 1.0" as SQLSvr1 {
Microsoft
}
object "Microsoft Server 4.2 for NT" as SQLSvr4NT {
Microsoft
}
object "Sybase Adaptive Server Enterprise" as SybaseASE {
Sybase -> SAP SE
}
object "Microsoft Server 6.0" as SQLSvr6 {
Microsoft
}
object "Microsoft Server 7.0" as SQLSvr7 {
Microsoft
}
object "Microsoft SQL Server 2005" as SQLSvr2005 {
Microsoft
}
object "Microsoft SQL Server 2017 for Linux" as SQLSvr2017 {
Microsoft
}
object "SAP Sybase Adaptive Server Enterprise" as SAPSybASE {
SAP SE
}

uIngres --> Sybase : 1984
Sybase --> SQLServer : 1988
SQLServer --> SQLSvr1 : 1989
SQLSvr1 --> SQLSvr4NT : 1993
SQLServer ---> SybaseASE : 1995
SQLSvr4NT -->  SQLSvr6 : 1995
SQLSvr6 --> SQLSvr7 : 1998
SQLSvr7 <|--- SQLSvr2005: 2005
SybaseASE ...> SAPSybASE : 2010
SQLSvr2005 ---> SQLSvr2017 : 2017




object POSTGRES {
U.C. Berkeley
}
object "POSTGRES 2" as PG2 {
U.C. Berkeley
}
object "POSTGRES 4.2" as PG4 {
U.C. Berkeley
}
object "Postgres95" as Pg95 {
U.C. Berkeley
}

object Illustra {
Illustra Information Technologies
}
object "MARIPOSA" as Mariposa {
U.C. Berkeley
}
object Cohera {
Cohera Corporation
}
object "Enterprise Catalog Management" as PeopleSoft {
PeopleSoft
}
object "PeopleSoft 9.0 " as PeopleSoft9 {
Oracle Corporation
}

object "PostgreSQL 6.0" as Pg6 {
PGDG:
PostgreSQL Gloval Development Group
}
object "PostgreSQL 7.0" as Pg7 {
PGDG
}
object "PowerGRES" as PwGRES {
SRAOSS/Fujitsu
}
object "PostgreSQL 8.0"  as Pg8 {
PGDG
}
object "EDB Postgres Plus" as EDBPg {
EnterpriseDB
}
object "BizGres" as BiGres {
GreenPlum
}
object "PostgreSQL 9.0" as Pg9 {
PGDG
}
object "PostgreSQL 10" as Pg10 {
PGDG
}
object "PostgreSQL 11" as Pg11 {
PGDG
}
object "PostgreSQL 12" as Pg12 {
PGDG
}

Mike <|.. POSTGRES
Mike <|.. Illustra
uIngres <|-- POSTGRES : 1986
POSTGRES --> PG2 : 1988
PG2 --> PG4 : 1994
PG4 --> Pg95 : 1995
PG4 --> Illustra : 1995
Illustra .* Informix9 : 1996
Pg95 --> Pg6 : 1997
Pg7 <|-- EDBPg : 2005
Pg6 --> Pg7 : 2000
Pg7 <|- PwGRES : 2003
Pg7 <|-- BiGres : 2005
Pg7 --> Pg8 : 2005
Pg8 --> Pg9 : 2010
Pg9 --> Pg10 : 2017
Pg10 --> Pg11 : 2018
Pg11 --> Pg12 : 2019

Mike <|.. Mariposa
Pg95 --* Mariposa : 1996
Mariposa --> Cohera : 1996
Cohera ..* PeopleSoft : 2001
PeopleSoft ..* PeopleSoft9: 2005

object "IBM ISAM" as ISAM {
ISAM: System/360
Indexed Sequential Access Method
} 
object "IBM VSAM" as VSAM {
ISAM: System/370
Virtual Sequential Access Method
} 
object "INFORMIX" as informix {
Informix(INFORMation unIX) Corporation
}
object "INFORMIX-SQL 1.1" as Informix1 {
Informix Corporation
}
object "INFORMIX-OnLine 5" as Informix5 {
Informix Corporation
}
object "Informix Dynamic Server V7" as Informix7 {
Informix Corporation
}
object "Informix Universal Server V9" as Informix9 {
Informix Corporation
}
object "Informix Universal Server V10" as Informix10 {
Informix Corporation
}
object "Informix Dynamic Server V11.5" as Informix11 {
IBM Corporation
}

object "Informix V12.10" as Informix12 {
IBM Corporation
}

object "Informix V14.10" as Informix14 {
IBM Corporation
}

ISAM --> VSAM : 1975
VSAM <|-- informix : 1981
informix --> Informix1 : 1985
Informix1 --> Informix5 : 1990
Informix5 --> Informix7 : 1994
Informix7 --> Informix9 : 1996
Informix9 ---> Informix10 : 2005
Informix10 ---> Informix11 : 2008
Informix11 ---> Informix12 : 2013
Informix12 --> Informix14 : 2019




object "San Jose Research Laboratory" as SanJose {
IBM San Jose Research Laboratory
}
object "System R" as SystemR {
IBM San Jose Research Laboratory
}
object "SQL/DS" as SqlDS {
IBM Corporation
}
object "DB/2" as DB2 {
IBM Corporation
}
object "DB2 for OS/390 V5" as DB2v5 {
IBM Corporation
}
object "DB2 Universal Database V7" as DB2v7 {
IBM Corporation
}
object "DB2 for z/OS V8" as DB2v8 {
IBM Corporation
}
object "DB2 (MySQL engine) for Syste i V9" as DB2v9 {
IBM Corporation
}
object "DB2 V10" as DB2v10 {
IBM Corporation
}
object "DB2 LUW 11.1" as DB2v11 {
IBM Corporation
}
object "Db2(DB2 and dashDB)" as Db2 {
IBM Corporation
}
object "Db2 AI Database 11.5" as Db2AI {
IBM Corporation
}

Ted <|--SanJose : 1973
SanJose <|.. SystemR : 1977
SystemR --> SqlDS : 1981
SqlDS --> DB2 : 1983
DB2 --> DB2v5 : 1997
DB2v5 --> DB2v7 : 2001
Informix9 ..* DB2v7  : 2001
DB2v7 --> DB2v8  : 2004
DB2v8 --> DB2v9 : 2009
DB2v9 --> DB2v10 : 2012
DB2v10 --> DB2v11 : 2016
DB2v11 --> Db2 :2017
Db2 --> Db2AI : 2019



object "Lawrence Joseph Ellison" as Larry {
Software Development Laboratories
}
object "Oracle V2" as Oracle2 {
Oracle Corporation
}
object "Oracle 5.0" as Oracle5 {
Oracle Corporation
}
object "Oracle7" as Oracle7 {
Oracle Corporation
}
object "Oracle7.3 Object-relational" as Oracle73 {
Oracle Corporation
}
object "Oracle8i" as Oracle8i {
Oracle Corporation
}
object "Oracle9i" as Oracle9i {
Oracle Corporation
}
object "Oracle10g" as Oracle10g {
Oracle Corporation
}
object "Oracle11g" as Oracle11g {
Oracle Corporation
}
object "Oracle12c" as Oracle12c {
Oracle Corporation
}
object "Oracle18c" as Oracle18c {
Oracle Corporation
}
object "Oracle19c" as Oracle19c {
Oracle Corporation
}

SanJose <|-- Larry : 1977
Larry ..> Oracle2 : 1977
Oracle2 --> Oracle5 : 1985
Oracle5 --> Oracle7 : 1992
Oracle7 --> Oracle73 : 1996
Oracle73 --> Oracle8i : 1998
Oracle8i --> Oracle9i : 2001
Oracle9i --> Oracle10g : 2003
Oracle10g --> Oracle11g : 2007
Oracle11g --> Oracle12c : 2013
Oracle12c --> Oracle18c : 2018
Oracle18c --> Oracle19c : 2019



@enduml

