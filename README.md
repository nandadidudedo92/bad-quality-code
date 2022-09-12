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

>this kind of variable declaration can be dangerous, because the global data can be changed from multiple method and multiple call on busy trafic. We can fix it like code bellow


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
    public CommonResponse<Object> getData(@RequestParam Long id)   {
        try {
            return commonResponse.successHandler(objectService.getDataById(id));
        } catch (Exception e) {
            return commonResponse.errorHandler(e.getMessage());
        }
    }

## **Method Too Much IF**

    public String processing(String name, String color, Object object1, OtherObject object2, int width ){
        String mustReturnThisValue = "";

            if(name.toUpperCase(Locale.ROOT).contains("RICK")) {
                ..
                someprocess
                ..
            } else if(name.toUpperCase(Locale.ROOT).contains("JOHN")) {
                ..
                someprocess
                ..
            } else {
                 ..
                someprocess
                ..
            }
        

            if(color.toUpperCase(Locale.ROOT).contains("RED")) {
                 ..
                someprocess
                ..
            } else if(color.toUpperCase(Locale.ROOT).contains("GREEN")) {
                 ..
                someprocess
                ..
            } else {
                 ..
                someprocess
                ..
            }

            if(object1.getIntProperty() < 50 ) {
                if(object1.getIntProperty() < 10 ) {
                 ..
                someprocess
                ..
            } else if (object1.getIntProperty() < 20 ) {
                 ..
                someprocess
                ..
            } else if (object1.getIntProperty() < 30 ) {
                 ..
                someprocess
                ..
            } else if (object1.getIntProperty() < 40 ) {
                 ..
                someprocess
                ..
            } else  {
                 ..
                someprocess
                ..
                } 
            } else {
                 ..
                someprocess
                ..
            }
        
            if(object2.getIntProperty() < 50 ) {
            if(object2.getIntProperty() < 10 ) {
                 ..
                someprocess
                ..
            } else if (object2.getIntProperty() < 20 ) {
                 ..
                someprocess
                ..
            } else if (object2.getIntProperty() < 30 ) {
                 ..
                someprocess
                ..
            } else if (object2.getIntProperty() < 40 ) {
                 ..
                someprocess
                ..
            } else  {

                }
            } else {
                 ..
                someprocess
                ..
            }
        

        if(object1.getIntProperty() < 50 ) {
            if(width < 10)) {
                 ..
                someprocess
                ..
            } else if (width < 20) {
                 ..
                someprocess
                ..
            }else {
            ..
                someprocess
                ..
            }
            
        } else {
                 ..
                someprocess
                ..
            }

        return mustReturnThisValue;
    }

> this code is called a complex code, we can refractore the code to make this code better, even if this code have to many condition.


*create method to become a part of process*

    public String checkName(String name) {
    if(name.toUpperCase(Locale.ROOT).contains("RICK")) {
                ..
                someprocess
                ..
            } else if(name.toUpperCase(Locale.ROOT).contains("JOHN")) {
                ..
                someprocess
                ..
            } else {
                 ..
                someprocess
                ..
            }
    }

    public String checkColor(String color) {
    if(color.toUpperCase(Locale.ROOT).contains("RED")) {
                 ..
                someprocess
                ..
            } else if(color.toUpperCase(Locale.ROOT).contains("GREEN")) {
                 ..
                someprocess
                ..
            } else {
                 ..
                someprocess
                ..
            }
    }


    public String checkObject1(Object1 object1) {
    if(object1.getIntProperty() < 50 ) {
                if(object1.getIntProperty() < 10 ) {
                 ..
                someprocess
                ..
            } else if (object1.getIntProperty() < 20 ) {
                 ..
                someprocess
                ..
            } else if (object1.getIntProperty() < 30 ) {
                 ..
                someprocess
                ..
            } else if (object1.getIntProperty() < 40 ) {
                 ..
                someprocess
                ..
            } else  {
                 ..
                someprocess
                ..
                } 
            } else {
                 ..
                someprocess
                ..
            }
    }

    public String checkObject2(Object2 object2) {
    if(object2.getIntProperty() < 50 ) {
            if(object2.getIntProperty() < 10 ) {
                 ..
                someprocess
                ..
            } else if (object2.getIntProperty() < 20 ) {
                 ..
                someprocess
                ..
            } else if (object2.getIntProperty() < 30 ) {
                 ..
                someprocess
                ..
            } else if (object2.getIntProperty() < 40 ) {
                 ..
                someprocess
                ..
            } else  {

                }
            } else {
                 ..
                someprocess
                ..
            }
    }


    public String checkWidth(int width) {
            if(width < 10)) {
                 ..
                someprocess
                ..
            } else if (width < 20) {
                 ..
                someprocess
                ..
            }else {
            ..
                someprocess
                ..
            }
            
        } else {
                 ..
                someprocess
                ..
            }
    }

> edit the method 

    public String processing(String name, String color, Object object1, OtherObject object2, int width ){

        checkName(name);
        checkColor(color);
        checkObject1(object1);
        checkObject2(object2);
        checkWidth(width);

        some process and return;
    }

## **LONG LIST PARAMETER**

    public String processMenu(String name, String color, String source,  int width, int Long, String taste, String cameFrom ){
        .....
    }

>  make those param simple, or make object first 

    public String processMenu(Food food){
        .....
    }

## **HARDCODED VARIABLE**

    private String nameOfInstances = "INSTANCES_1";
    private int maxFileSize = 2048;

> this kind of code make static data and hard to manage

    always use database to store that kind of configuration we can make an sys_app_config table to store those data. and when we want using it, we can call it from database;

    public String getSomeVariable() {
        appConfig.repository.getByKey("MAX_FILE_SIZE");
        appConfig.repository.getByKey("NAME_OF_INSTANCES");
    }

