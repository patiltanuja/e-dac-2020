const csv = require('csv-parser')
const fs = require('fs')
const mysql = require('mysql');
const results = [];
const createCsvWriter = require('csv-writer').createArrayCsvWriter;
const csvWriter = createCsvWriter({
header: ['result'],
path: 'backendurl1.csv'
});
var mysqlConnection = mysql.createConnection({
connectionLimit: 100,
host: '10.140.38.12',
user: 'root',
password: '7layer',
database: 'ssg',
port: 3306,
multipleStatements: true
});
mysqlConnection.connect((err,) => {
if (!err)
console.log('DB connection succeded.');
else
console.log('DB connection failed \n Error : ' + JSON.stringify(err, undefined, 2));
});
/*
fs.createReadStream('apilist.csv')
.pipe(csv({ headers: false }))
.on('data', (data) => results.push(data['0']))
.on('end', () => {
results.forEach((element) => {
mysqlConnection.query('SELECT p.xml FROM published_service ps JOIN policy p ON ps.policy_goid=p.goid where ps.routing_uri="' + element + '"', (err, rows, fields) => {
if (!err) {
rows = JSON.stringify(rows)
var regex = //g
let myArray;
myArray = rows.match(regex)
// console.log(myArray)
myArray = JSON.stringify(myArray)
myArray = myArray.split('"')[2].split("\\")[0]
// console.log(element, myArray)
// myArray = JSON.stringify(myArray)
// var domain = myArray.split("=")[1].split("/")[0].replace(/[&\/\\#,+()$~%'":*?<>{}]/g,'').replace(/gateway./g,'');
// console.log(domain)
// var contextpath = myArray.split("=")[1].split("/")[1].replace(/[&\/\\#,+()$~%'":*?<>{}]/g,'').replace(/gateway./g,'');
// console.log(contextpath)
// mysqlConnection.query('SELECT propkey,propvalue from cluster_properties limit 8;', (err, rows, fields) => {
// if (!err) {
// // console.log(rows)
// var backendURL =rows[0].propvalue+"/"+rows[1].propvalue
// console.log(rows[0].propvalue+"/"+rows[1].propvalue)
// console.log(element,backendURL)

const records = [
[element, myArray]];
csvWriter.writeRecords(records) // returns a promise
.then(() => {
});
// }else{
// console.log(err)
// } 
// }); 

}
else {
console.log(err)
}
});
});
});
*/
// var fs = require('fs')
function escapeRegExp(string) {
return string.replace(/[.*+\-?^${}()|[\]\\]/g, '\\$&'); // $& means the whole matched string
}
fs.readFile('backendurl.csv', 'utf8', function (err, data) {
if (err) {
return console.log(err);
}

var result = ''
mysqlConnection.query('SELECT propkey,propvalue from cluster_properties;', (err, rows, fields) => {
if (!err) {

rows.forEach((element) => {
console.log(element.propkey)
const key = '${gateway.' + element.propkey + '}'
console.log(key)
// var find = 'abc';
var re = new RegExp(escapeRegExp(key), 'g');
result = data.replace(re, element.propvalue);
console.log(result+"----------------------------------")
fs.writeFileSync('backendurl1.csv', result, 'utf8', function (err) {
if (err) return console.log(err);
});
})
console.log('atfer for loop---------------------------'+result)
} else {
console.log(err)
}
});
console.log(result)


});
