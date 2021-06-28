properties([parameters([password(name: 'KEY',description : 'Encryption Key'), string(defaultValue: 'password', name: 'pwd', trim: true)])])

//     stage('Build') {
        
      
// //         writeCSV file: 'output.csv', records: records, format: CSVFormat.EXCEL
// //         def records = [['${params.KEY}', '${params.pwd}']]
// //         writeCSV file: 'output.csv', records: records, format: CSVFormat.EXCEL
// //         def amap = ["${params.KEY}" : "${params.pwd}"]
           
//            def records = readCSV file: 'test/output.csv'
//            assert records[0][0] == 'key'
//            assert records[1][1] == 'b'


//     }
pipeline {
   agent { label 'master' }
   stages {
    stage('write') {
           steps {
               script {
                   def records = [['key', 'value'], ['a', 'b']] 
                   
                   writeFile(file: 'output.csv', text: data)
                   sh "ls -l"
               }
           }
       }
       stage('read') {
           steps {
               script {
                   def data = readFile(file: 'output.csv')
                   println(data)
               }
           }
       }
   }

}
    
