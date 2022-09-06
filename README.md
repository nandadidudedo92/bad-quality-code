# **BAD QUALITY CODE**
Bad Quality Code Explanation and how to fix it by **M@P**


## **Unrecognize Variable Name**
---

    String x = "This Should Be a Title of the Page";
    String y = "This Should be a Footer Notes of the Page";

    ...

    String z = "This Should Be a Title of the Page";

> avoid variable duplication like this with give a variable a proper name

    String titleText = "This Should Be a Title of the Page";
    String footerText = "This Should be a Footer Notes of the Page";

## **Using Global Variable to Store Data**
---
    

    public class ObjectService {

        DataObject dataObject;
        int globalIntVariable;
        OtherDataObject otherData;

        
        public DataObject addData(DataObject data, int intFromOtherClass) {

            dataObject = data;
            globalIntVariable = intFromOtherClass;
            .
            ........ data processing here ..........
            .
            dataObject.setSomeInt(globalIntVariable)
            return dataObject;

        }

        
        public DataObject otherDataProcess(DataObject data, int intFromOtherClass, OtherDataObject otherData) {

            dataObject = data;
            globalIntVariable = intFromOtherClass;
            otherDataObject = otherData;
            .
            ........ data processing here ..........
            .
            return dataObject;

        }

    }

>this kind of declaration variable can be dangerous, because the global data can be changed from multiple method and multiple call on busy trafic. We can fix it like code bellow


    public class ObjectService {

        public DataObject addData(DataObject data, int intFromOtherClass) {

        DataObject dataObject = data;
        int globalIntVariable = intFromOtherClass;
            .
            ........ data processing here ..........
            .
            dataObject.setSomeInt(globalIntVariable)
            return dataObject;

        }

        
        public DataObject otherDataProcess(DataObject data, int intFromOtherClass, OtherDataObject otherData) {


        DataObject dataObject = data;
        int globalIntVariable = intFromOtherClass;
        OtherDataObject = otherData;
            .
            ........ data processing here ..........
            .
            return dataObject;

        }

    }



## **CONTROLLER CALL REPOSITORIES DIRECTLY**
---

    @RestController
    @RequestMapping(value = "data")
    public class DataController {

        @Autowired
        DataRepository dataRepository;

        @GetMapping("get-by-param")
        public DataEntity getDataBySomething(@RequestMapping String param) {
            return dataRepository.findBySomething(param);
        }

    }

> This kind of code cause no business process layer (Service Layer) to processing data. We can use service layer like code below

*make DataService Interface first*

    public interface DataService {

        DataEntity getDataWithProcess(String param)
    }

*make DataServiceImpl to implement the interface*

    @Service
    public class DataServiceImpl implements DataService {

        @Autowired
        DataRepository

        @Override
        public DataEntity getDataWithProcess(String param) {

            DataEntity data =  dataRepository.findBySomething(param);
            data.setProcessedData(data.getSize()*data.getLength);

            return data;
        }

    }

*edit the Controller*

    @RestController
    @RequestMapping(value = "data")
    public class DataController {

        @Autowired
        DataService dataService;

        @GetMapping("get-by-param")
        public DataEntity getDataBySomething(@RequestMapping String param) {
            return dataService.getDataWithProcess(param);
        }

    }


## **USING CONTROLLER TO PROCESS DATA**
    
    @RestController
    @RequestMapping(value = "data")
    public class DataController {

        @Autowired
        DataService dataService;

        @GetMapping("get-by-param")
        public DataEntity getDataBySomething(@RequestMapping String param) {

            DataEntity data = dataService.getDataWithProcess(param);


            Integer totalSize = data.getProcessedData() * data.getTotalData();
            Double averageLength = (Double) totalSize / data.getLength();
            Double otherProcess = (Double)totalSize * averageLength * (Double) data.getSize(); 

            data.setTotalSize(totalSize);
            data.setAverageLength(averageLength);
            data.setOtherProcess(otherProcess)
        
        return data;
        }
    }

> this kind of controller make controller to long, we can modified controller and put the process in service layer

*edit the service layer*

    
        @Override
        public DataEntity getDataWithProcess(String param) {

            DataEntity data =  dataRepository.findBySomething(param);
            data.setProcessedData(data.getSize()*data.getLength);

            Integer totalSize = data.getProcessedData() * data.getTotalData();
            Double averageLength = (Double) totalSize / data.getLength();
            Double otherProcess = (Double)totalSize * averageLength * (Double) data.getSize(); 

            data.setTotalSize(totalSize);
            data.setAverageLength(averageLength);
            data.setOtherProcess(otherProcess)

            return data;
        }

*edit the controller layer

    @GetMapping("get-by-param")
        public DataEntity getDataBySomething(@RequestMapping String param) {
            return dataService.getDataWithProcess(param);
        }

## **METHOD BODY IS TOO LARGE**

        @Override
        public DataEntity getDataWithProcess(String param) {

            DataEntity data =  dataRepository.findBySomething(param);
            data.setProcessedData(data.getSize()*data.getLength);

            Integer totalSize = data.getProcessedData() * data.getTotalData();
            Double averageLength = (Double) totalSize / data.getLength();
            Double otherProcess = (Double)totalSize * averageLength * (Double) data.getSize(); 

            data.setTotalSize(totalSize);
            data.setAverageLength(averageLength);
            data.setOtherProcess(otherProcess)

            if(totalSize > 999) {
                .
                .
                .
                otherhAddProcess
                .
                .
                .
            } else {
                .
                .
                .
                anotherAddProcess
                .
                .
                .
            }

            return data;
        }

> Somethimes in complicated business process, we make too large method. To avoid that, we can create some method to simplified and make method little.

    private DataEntity otherAddProcess(data) {
                .
                .
                .
                otherhAddProcess
                .
                .
                .
    }

    private DataEntity anotherAddProcess(data) {
                .
                .
                .
                anotherAddProcess
                .
                .
                .
    }

> edit the first method

        @Override
        public DataEntity getDataWithProcess(String param) {

            DataEntity data =  dataRepository.findBySomething(param);
            data.setProcessedData(data.getSize()*data.getLength);

            Integer totalSize = data.getProcessedData() * data.getTotalData();
            Double averageLength = (Double) totalSize / data.getLength();
            Double otherProcess = (Double)totalSize * averageLength * (Double) data.getSize(); 

            data.setTotalSize(totalSize);
            data.setAverageLength(averageLength);
            data.setOtherProcess(otherProcess)

            if(totalSize > 999) {
                otherhAddProcess(data)
            } else {
                anotherAddProcess(data)
            }

            return data;
        }

## **THE COMMON PROCESS FOR MULTIPLE METHOD BODY **



## **CONTROLLER DID NOT HANDLE AN ERROR**
---

    @GetMapping("get-by-param")
        public DataEntity getDataBySomething(@RequestMapping String param) {
            return dataService.getDataWithProcess(param);
        }

> This controller will return error 500 WhitelabelErrorPage when processing data if error happens. 
> Some client or front end want don't want  error 500 on http status
> There are many options to handle this error, first we will try to send null data as a response like this.

    @GetMapping(value = "data")
    public TransactionDataWrapper getData(@RequestParam Long id)   {
        try {
            return transactionDataService.findById(id);
        } catch (Exception e) {
            return new TransactionDataWrapper();
        }
    }



