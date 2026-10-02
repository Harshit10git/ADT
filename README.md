Index.js

import { connectToMongoDB ,disconnectFromMongoDB } from "./src/config/database.js";
import Students from "./src/config/model/student.model.js";

async function run(params) {
    try {
        await connectToMongoDB();
        const Student = await Students.insertMany([
            {
                studentId: "S01",
                name: "Asha",
                age: 21,
                course:"MScIT",
                marks:82,
                city:"Surat"
            },
            {
            studentId: "S02",
            name: "Ravi",
            age: 23,
            course: "MScIT",
            marks: 58,
            city: "Ahemdabad"
            },
            {
                studentId:"S03",
                name:"Neha",
                age:20,
                course:"BCA",
                marks:91,
                city:"Surat"
            },
            {
                studentId:"S04",
                name:"Imran",
                age:22,
                course:"MScIT",
                marks:70,
                city:"Vadodra"
            },
            {
                studentId:"S05",
                name:"Kavya",
                age:24,
                course:"BCA",
                marks:45,
                city:"Rajkot"
            }

        ]);
        console.log('${Student.length} Record Inserted');
        console.log(Student);
        
        //display all

        const displayall = await Student.find();
        console.log("All Students:");
        console.log(displayall);

        //city surat

        const findcity= await Student.find({city:"Surat"});
        console.log("Students whose city is Surat:");
        console.log(findcity);

        //display students whose marks is atleast 70 and from highest to lowest

        const studentmark= await Student.find({marks:{$gte:70}}).sort({marks:-1});
        console.log("marks greater than 70");
        console.log(studentmark);

        //update student S02 mark to 65

        const updatemark= await Student.updateOne({studentId:"S02"},{$set:{marks:65}});
        console.log("Student S02 mark updated:",updatemark);
        const findS02 = await Student.find({studentId:"S02"});
        console.log("Updated student:", findS02);

        // update isActive false for BCA student

        const updateIsActive= await Student.updateMany({course:"BCA"},{$set:{isActive:false}});
        console.log(updateIsActive);
        const findUpdatedStudent = await Student.find({course:"BCA"});
        console.log("Updated students:", findUpdatedStudent);

        //delete S05 student

        const deletestudent = await Student.deleteone({studentId:"S05"});
        console.log("Delete student", deletestudent);
        const verify=await Student.find({studentId:"S05"});
        console.log("verify deleted student", verify);

    } catch (error) {
        if (error.name === "ValidationError") {
            console.log("Validation error", error.message);
        } else {
            console.error("Error:", error);
            process.exitCode = 1;
        }
        
    }finally{
          disconnectFromMongoDB();
    }
    
}
run();


database.js

import mongoose from 'mongoose';

export async function connectToMongoDB() {
  try {
    await mongoose.connect("mongodb+srv://harshitgheewala862_db_user:RSynF7S5dJEFCCWh@cluster0.h5m6vu3.mongodb.net/?appName=Cluster0",{dbName:"Students"});
    console.log("You successfully connected to MongoDB!");
    return mongoose;
  } catch (err) {
    console.dir(err);
  }
}

// Call this only when your application terminates
export async function disconnectFromMongoDB() {
  await mongoose.connection.close();
}

student.module.js

import mongoose from "mongoose";

const studentsSchema = new mongoose.Schema({
    studentId:{
        type:String,
        require:true,
        unique:true
    },
    name:{
        type:String,
        require:true
    },
    age:{
        type:Number,
        require:true,
        min:18
    },
    course:{
        type:String,
        require:true,
        enum:["MScIT","BCA"]
    },
    marks:{
        type:Number,
        require:true,
        min:0,
        max:100
    },
    city:{
        type:String,
        require:true
    },
    isActive:{
        type:Boolean,
        default:true
    }
});
export default mongoose.model("Students",studentsSchema);


