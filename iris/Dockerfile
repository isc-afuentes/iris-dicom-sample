FROM store/intersystems/irishealth-community:2020.2.0.211.0

USER ${ISC_PACKAGE_MGRUSER}

COPY --chown=$ISC_PACKAGE_MGRUSER:$ISC_PACKAGE_IRISGROUP irissession.sh /
RUN chmod +x /irissession.sh

# copy files
COPY --chown=$ISC_PACKAGE_MGRUSER:$ISC_PACKAGE_IRISGROUP src /tmp/src
COPY --chown=$ISC_PACKAGE_MGRUSER:$ISC_PACKAGE_IRISGROUP install /tmp/install

RUN ls -l /tmp

SHELL ["/irissession.sh"]

RUN \
  zn "USER" \
  # load & compile source code
  do $SYSTEM.OBJ.LoadDir("/tmp/src", "ck", , 1) \
  # load DICOM config
  do ##class(EnsLib.DICOM.Util.AssociationContext).ImportXML("/tmp/install/uro-dicom.xml") \
  # start production
  set production = "DICOM.Production" \
  set ^Ens.Configuration("csp","LastProduction") = production \
  do ##class(Ens.Director).SetAutoStart(production) \
  set sc = 1

# bringing the standard shell back
SHELL ["/bin/bash", "-c"]