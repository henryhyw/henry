
EVALUATE
VAR _Columns =
    SELECTCOLUMNS(
        FILTER(
            INFO.VIEW.COLUMNS(),
            [Table] IN { "TABLE_1", "TABLE_2" }
        ),
        "TableName", [Table] & "",
        "ColumnName", [Name] & "",
        "SourceColumn", [SourceColumn],
        "ColumnFormula", [Expression]
    )

VAR _Dependencies =
    SELECTCOLUMNS(
        FILTER(
            INFO.CALCDEPENDENCY(),
            [REFERENCED_OBJECT_TYPE] = "COLUMN"
                && [REFERENCED_TABLE] IN { "TABLE_1", "TABLE_2" }
        ),
        "TableName", [REFERENCED_TABLE] & "",
        "ColumnName", [REFERENCED_OBJECT] & "",
        "UsedByType", [OBJECT_TYPE],
        "UsedByTable", [TABLE],
        "UsedByObject", [OBJECT],
        "Formula", [EXPRESSION]
    )

VAR _Joined =
    NATURALLEFTOUTERJOIN(
        _Columns,
        _Dependencies
    )

RETURN
    SELECTCOLUMNS(
        _Joined,
        "Table", [TableName],
        "Column", [ColumnName],
        "DAX Referenced",
            IF(ISBLANK([UsedByType]), "No match", "Yes"),
        "Used By Type", [UsedByType],
        "Used By Table", [UsedByTable],
        "Used By Object", [UsedByObject],
        "Formula", [Formula],
        "Source Column", [SourceColumn],
        "Column Formula", [ColumnFormula]
    )

ORDER BY [Table], [Column], [Used By Object]
