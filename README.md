# employeewebapi
Employee.cs

namespace employeeWebApi.Models
{
    public class Employee
    {
        public int Id { get; set; }
        public required string Name { get; set; }    
        public string Position { get; set; }
        public required string Department { get; set; }
    }
}

-------------------------------------------------------------------
EmployeeController.cs

using employeeWebApi.Models;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Configuration;
using MySql.Data.MySqlClient;
using System.Collections.Generic;
using System.Data;
using employeeWebApi.Models;

namespace employeeWebApi.Controllers
{
    [Route("api/[controller]")]
    [ApiController]
    public class EmployeeController : ControllerBase
    {
        private readonly IConfiguration _configuration;

        public EmployeeController(IConfiguration configuration)
        {
            _configuration = configuration;
        }

        // GET: api/Employee
        [HttpGet]
        public IEnumerable<Employee> GetEmployees()
        {
            List<Employee> employees = new List<Employee>();

            using (MySqlConnection conn = new MySqlConnection(_configuration.GetConnectionString("DefaultConnection")))
            {
                conn.Open();
                string query = "SELECT * FROM employee";
                MySqlCommand cmd = new MySqlCommand(query, conn);
                MySqlDataReader reader = cmd.ExecuteReader();

                while (reader.Read())
                {
                    employees.Add(new Employee
                    {
                        Id = reader.GetInt32("id"),
                        Name = reader.GetString("Name"),
                        Position = reader.GetString("Position"),
                        Department = reader.GetString("Department")
                    });
                }
            }

            return employees;
        }

        // GET: api/Employee/5
        [HttpGet("{id}")]
        public ActionResult<Employee> GetEmployeeById(int id)
        {
            Employee employee = null;

            using (MySqlConnection conn = new MySqlConnection(_configuration.GetConnectionString("DefaultConnection")))
            {
                conn.Open();
                string query = "SELECT * FROM employee WHERE id = @id";
                MySqlCommand cmd = new MySqlCommand(query, conn);
                cmd.Parameters.AddWithValue("@id", id);
                MySqlDataReader reader = cmd.ExecuteReader();

                if (reader.Read())
                {
                    employee = new Employee
                    {
                        Id = reader.GetInt32("Id"),
                        Name = reader.GetString("Name"),
                        Position = reader.GetString("Position"),
                        Department = reader.GetString("Department")
                    };
                }
            }

            if (employee == null)
            {
                return NotFound();
            }

            return employee;
        }

        // POST: api/Employee
        [HttpPost]
        public ActionResult CreateEmployee([FromBody] Employee employee)
        {
            using (MySqlConnection conn = new MySqlConnection(_configuration.GetConnectionString("DefaultConnection")))
            {
                conn.Open();
                string query = "INSERT INTO employee (Name, Position, department) VALUES (@Name, @Position, @Department)";
                MySqlCommand cmd = new MySqlCommand(query, conn);
                cmd.Parameters.AddWithValue("@Name", employee.Name);
                cmd.Parameters.AddWithValue("@Position", employee.Position);
                cmd.Parameters.AddWithValue("@Department", employee.Department);
                cmd.ExecuteNonQuery();
            }

            return Ok();
        }

        // PUT: api/Employee/5
        [HttpPut("{id}")]
        public ActionResult UpdateEmployee(int id, [FromBody] Employee employee)
        {
            using (MySqlConnection conn = new MySqlConnection(_configuration.GetConnectionString("DefaultConnection")))
            {
                conn.Open();
                string query = "UPDATE employee SET Name = @Name,  Position = @Position, Department = @Department WHERE id = @id";
                MySqlCommand cmd = new MySqlCommand(query, conn);
                cmd.Parameters.AddWithValue("@Id", id);
                cmd.Parameters.AddWithValue("@Name", employee.Name);
                cmd.Parameters.AddWithValue("@Position", employee.Position);
                cmd.Parameters.AddWithValue("@Department", employee.Department);
                cmd.ExecuteNonQuery();
            }

            return Ok();
        }

        // DELETE: api/Employee/5
        [HttpDelete("{id}")]
        public ActionResult DeleteEmployee(int id)
        {
            using (MySqlConnection conn = new MySqlConnection(_configuration.GetConnectionString("DefaultConnection")))
            {
                conn.Open();
                string query = "DELETE FROM employee WHERE id = @id";
                MySqlCommand cmd = new MySqlCommand(query, conn);
                cmd.Parameters.AddWithValue("@id", id);
                cmd.ExecuteNonQuery();
            }

            return Ok();
        }
    }
}
--------------------------------------------------------------------------------------------------
