# standard-report-for-ekko-ekpo-mktr-lfa1- code pushdown without use all for enteries
standard report for ( ekko ekpo mktr lfa1 ) tables and code push down
TABLES: ekko, ekpo, makt, lfa1.

SELECTION-SCREEN: BEGIN OF BLOCK f1 WITH FRAME TITLE TEXT-001.
  SELECT-OPTIONS: s_e1 FOR ekko-ebeln.
SELECTION-SCREEN: END OF BLOCK f1.


TYPES: BEGIN OF ty_output,
         ebeln TYPE ekko-ebeln,
         ebelp TYPE ekpo-ebelp,
         lifnr TYPE ekko-lifnr,
         bedat TYPE ekko-bedat,
         name1 TYPE lfa1-name1,
         matnr TYPE ekpo-matnr,
         maktx TYPE makt-maktx,
         menge TYPE ekpo-menge,
         meins TYPE ekpo-meins,
         netwr TYPE ekpo-netwr,
         netpr TYPE ekpo-netpr,
       END OF ty_output.

DATA: lt_final TYPE TABLE OF ty_output.

SELECT e1~ebeln,
       e2~ebelp,
       e1~lifnr,
       e1~bedat,
       e4~name1,
       e2~matnr,
       e3~maktx,
       e2~menge,
       e2~meins,
       e2~netwr,
       e2~netpr
  FROM ekko AS e1
  INNER JOIN ekpo AS e2 ON e1~ebeln = e2~ebeln
  INNER JOIN makt AS e3 ON e2~matnr = e3~matnr
  INNER JOIN lfa1 AS e4 ON e1~lifnr = e4~lifnr
  INTO TABLE @lt_final
  WHERE e1~ebeln IN @s_e1.


IF lt_final IS NOT INITIAL.
  DATA: it_ft TYPE slis_t_fieldcat_alv,
        wa_ft TYPE slis_fieldcat_alv.

  wa_ft-col_pos = 1.
  wa_ft-fieldname = 'EBELN'. " Must be UPPERCASE
  wa_ft-seltext_m = 'Purchasing Document Number'.
  APPEND wa_ft TO it_ft.
  CLEAR wa_ft.

  wa_ft-col_pos = 2.
  wa_ft-fieldname = 'EBELP'.
  wa_ft-seltext_m = 'Item Number of Purchasing Document'.
  APPEND wa_ft TO it_ft.
  CLEAR wa_ft.

  wa_ft-col_pos = 3.
  wa_ft-fieldname = 'LIFNR'.
  wa_ft-seltext_m = 'Account Number of Vendor'.
  APPEND wa_ft TO it_ft.
  CLEAR wa_ft.

  wa_ft-col_pos = 4.
  wa_ft-fieldname = 'BEDAT'.
  wa_ft-seltext_m = 'Purchasing Document Date'.
  APPEND wa_ft TO it_ft.
  CLEAR wa_ft.

  wa_ft-col_pos = 5.
  wa_ft-fieldname = 'NAME1'.
  wa_ft-seltext_m = 'Name 1 of Vendor'.
  APPEND wa_ft TO it_ft.
  CLEAR wa_ft.

  wa_ft-col_pos = 6.
  wa_ft-fieldname = 'MATNR'.
  wa_ft-seltext_m = 'Material Number'.
  APPEND wa_ft TO it_ft.
  CLEAR wa_ft.

  wa_ft-col_pos = 7.
  wa_ft-fieldname = 'MAKTX'.
  wa_ft-seltext_m = 'Material Description'.
  APPEND wa_ft TO it_ft.
  CLEAR wa_ft.

  wa_ft-col_pos = 8.
  wa_ft-fieldname = 'MENGE'.
  wa_ft-seltext_m = 'Purchase Order Quantity'.
  APPEND wa_ft TO it_ft.
  CLEAR wa_ft.

  wa_ft-col_pos = 9.
  wa_ft-fieldname = 'MEINS'.
  wa_ft-seltext_m = 'Purchase Order Unit of Measure'.
  APPEND wa_ft TO it_ft.
  CLEAR wa_ft.

  wa_ft-col_pos = 10.
  wa_ft-fieldname = 'NETWR'.
  wa_ft-seltext_m = 'Net Order Value in PO Currency'.
  APPEND wa_ft TO it_ft.
  CLEAR wa_ft.

  wa_ft-col_pos = 11.
  wa_ft-fieldname = 'NETPR'.
  wa_ft-seltext_m = 'Net Price per Unit'.
  APPEND wa_ft TO it_ft.
  CLEAR wa_ft.



  CALL FUNCTION 'REUSE_ALV_GRID_DISPLAY'
    EXPORTING
      i_callback_program = sy-repid" Correct runtime program variable
      it_fieldcat        = it_ft
      i_default          = 'X'
    TABLES
      t_outtab           = lt_final
    EXCEPTIONS
      program_error      = 1
      others             = 2.

  IF sy-subrc <> 0.
    MESSAGE 'Error while displaying the ALV Grid' TYPE 'E'.
  ENDIF.
endif. 
