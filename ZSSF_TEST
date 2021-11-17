*&---------------------------------------------------------------------*
*& Report ZSSF_TEST 
*&---------------------------------------------------------------------*
*&
*&---------------------------------------------------------------------*
REPORT ZSSF_TEST.

PARAMETERS:
p_text TYPE string DEFAULT '123',
p_singid TYPE string DEFAULT 'CN=F-SK' LOWER CASE.

DATA:
  gv_data       TYPE xstring,
  gv_error      TYPE text80,
  gt_signer     LIKE STANDARD TABLE OF ssfinfo,
  gt_sign       LIKE STANDARD TABLE OF ssfbin,
  gt_data       LIKE gt_sign WITH HEADER LINE,
  gt_data_ex    LIKE gt_sign WITH HEADER LINE,
  gv_sign_len   LIKE ssfparms-sigdatalen,
  gv_crc        LIKE ssfparms-ssfcrc,
  gv_datalen    type i. "ssfparms-indatalen.

FIELD-SYMBOLS:
  <gs_signer>   LIKE LINE OF gt_signer.

START-OF-SELECTION.

  CALL FUNCTION 'SCMS_STRING_TO_XSTRING'
    EXPORTING
      text   = p_text
    IMPORTING
      buffer = gv_data
    EXCEPTIONS
      failed = 1
      OTHERS = 2.
  IF sy-subrc <> 0.
    MESSAGE ID sy-msgid TYPE sy-msgty NUMBER sy-msgno
               WITH sy-msgv1 sy-msgv2 sy-msgv3 sy-msgv4.
  ENDIF.

  CALL FUNCTION 'SCMS_XSTRING_TO_BINARY'
    EXPORTING
      buffer        = gv_data
    IMPORTING
      output_length = gv_datalen
    TABLES
      binary_tab    = gt_data.

  APPEND INITIAL LINE TO gt_signer ASSIGNING <gs_signer>.
  <gs_signer>-id = p_singid.

  CALL FUNCTION 'SSF_SIGN'
    EXPORTING
      ssf_dest                     = 'SAP_SSFATGUI'
*      b_inc_certs                  = 'X'
      b_inenc                      = 'X'
      ostr_input_data_l            = gv_datalen
    IMPORTING
      ostr_signed_data_l           = gv_sign_len
      crc                          = gv_crc
    TABLES
      ostr_input_data              = gt_data
      signer                       = gt_signer
      ostr_signed_data             = gt_sign
    EXCEPTIONS
      ssf_rfc_error                = 1
      ssf_rfc_no_memory            = 2
      ssf_rfc_get_data_error       = 3
      ssf_rfc_send_data_error      = 4
      ssf_rfc_signer_list_error    = 5
      ssf_rfc_input_data_error     = 6
      ssf_fb_input_parameter_error = 7
      ssf_rfc_destination_error    = 8
      OTHERS                       = 9.

  IF sy-subrc <> 0.
    MESSAGE ID sy-msgid TYPE sy-msgty NUMBER sy-msgno
               WITH sy-msgv1 sy-msgv2 sy-msgv3 sy-msgv4.
  ENDIF.

  IF gv_crc IS NOT INITIAL.
    CALL FUNCTION 'SSF_ERRORMESSAGE'
      EXPORTING
        crc       = gv_crc
        result    = 0
      IMPORTING
        errortext = gv_error
      EXCEPTIONS
        error     = 1
        others    = 2.

    WRITE : / `Ошибка получения подписи: `, gv_error.
    EXIT.
  ENDIF.

  CLEAR: gt_signer.

  CALL FUNCTION 'SSF_VERIFY'
    EXPORTING
      ssf_dest                     = 'SAP_SSFATGUI'
      b_inc_certs                  = 'X'
      ostr_signed_data_l           = gv_sign_len
      ostr_input_data_l            = gv_datalen
      str_pab                      = ''
      str_pab_password             = ''
    IMPORTING
      crc                          = gv_crc
    TABLES
      ostr_signed_data             = gt_sign
      ostr_input_data              = gt_data
      signer_result_list           = gt_signer
      ostr_output_data             = gt_data_ex
    EXCEPTIONS
      ssf_rfc_error                = 1
      ssf_rfc_no_memory            = 2
      ssf_rfc_get_data_error       = 3
      ssf_rfc_send_data_error      = 4
      ssf_rfc_input_data_error     = 5
      ssf_fb_input_parameter_error = 6
      ssf_rfc_destination_error    = 7
      OTHERS                       = 8.


  IF sy-subrc <> 0.
    MESSAGE ID sy-msgid TYPE sy-msgty NUMBER sy-msgno
               WITH sy-msgv1 sy-msgv2 sy-msgv3 sy-msgv4.
  ENDIF.

  IF gv_crc IS NOT INITIAL.
    CALL FUNCTION 'SSF_ERRORMESSAGE'
      EXPORTING
        crc       = gv_crc
        result    = 0
      IMPORTING
        errortext = gv_error
      EXCEPTIONS
        error     = 1
        others    = 2.

    WRITE : / `Ошибка сверки подписи: `, gv_error.
    EXIT.
  ENDIF.

  CLEAR: gv_data, p_text.

  CALL FUNCTION 'SCMS_BINARY_TO_XSTRING'
    EXPORTING
      input_length = gv_sign_len
    IMPORTING
      buffer       = gv_data
    TABLES
      binary_tab   = gt_sign
    EXCEPTIONS
      failed       = 1
      OTHERS       = 2.

  IF sy-subrc <> 0.
    MESSAGE ID sy-msgid TYPE sy-msgty NUMBER sy-msgno
               WITH sy-msgv1 sy-msgv2 sy-msgv3 sy-msgv4.
  ENDIF.

  WRITE `ЭП :` COLOR COL_HEADING.
  WRITE: / gv_data.
