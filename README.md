kata-string-calculator-ruby
===========================

Solución a la kata de solveet : http://www.solveet.com/exercises/Kata-String-Calculator/8, aprovechando el aprendizaje de Ruby, y utilizando TDD, con el framework de unit testing rspec de ruby.

```require "rspec"
#------------------------------
# CLASS STRINGCALCULATOR
#------------------------------
class StringCalculator
  def add numbers_string = ""
		@numbers_string = numbers_string
		@separators = get_separators
		@numbers = get_numbers
		unless has_negatives
			if @numbers.empty?
				0
			elsif @numbers.length == 1
				@numbers[0]
			else
				@numbers.inject {|acu, v| acu + v }
			end
		else
			raise Exception.new "Negativos no permitidos " + has_negatives.join(", ")
		end
	end

	def get_separators
		separators = [","]
		if @numbers_string =~ /^\/\/.*/
			sep = @numbers_string.split("//")[1].split("\n")[0]
			separators = sep.split("][").map { |v| v.gsub("[",""); }
			separators.map! { |v| v.gsub("]",""); }
		end
		separators
	end

	def get_numbers
		n_string = @numbers_string
		if n_string =~ /^\/\/.*/
			n_string = n_string.split("\n")[1]
		else
			n_string = n_string.gsub "\n" , @separators[0]
		end
		@separators.each { |sep| n_string = n_string.gsub sep, ","}
		numbers = n_string.split ","
		numbers.map! {|n| if n.to_i < 1000 then n.to_i else 0 end }
		numbers
	end

	def has_negatives
		negatives = []
		@numbers.each { |i| negatives << i if  i < 0 }
		if negatives.empty? then false else negatives end
	end
end

#------------------------------
# TESTS
#------------------------------
describe StringCalculator do
	before :each do
		@cal = StringCalculator.new
	end
	describe "#add" do
		it "Existe el metodo: existe el metodo add" do
			@cal.should be_an_instance_of StringCalculator
		end
		it "Numero de elementos de entrada: devuelve 0 para 0 parametros" do
			@cal.add.should eql 0
		end
		it "Numero de elementos de entrada: devuelve el parametro para 1 parametro" do
			@cal.add("1").should eql 1
		end
		it "Numero de elementos de entrada: devuelve 3 para 2 parametros (1,2)" do
			@cal.add("1,2").should eql 3
		end
		it "Numero de elementos de entrada: devuelve 10 para 4 parametros (1,2,3,4)" do
			@cal.add("1,2,3,4").should eql 10
		end
		it "Formato de delimitador: devuelve 6 para la entrada 1\\n2,3" do
			@cal.add("1\n2,3").should eql 6
		end
		it "Formato de delimitador: devuelve 6 para la entrada //;\\n1;2;3" do
			@cal.add("//;\n1;2;3").should eql 6
		end
		it "Formato de entrada negativa: devuelve error al pasarle negativos en la entrada -1,-2,3" do
			expect { @cal.add("-1,-2,3")}.to raise_exception {|exp| exp.message.should eql "Negativos no permitidos -1, -2" }
		end
		it "Formato de entrada grande: ignora numeros mayores de 1000 en la entrada 2,1001" do
			@cal.add("2,1001").should eql 2
		end
		it "Formato de tamaño delimitador: devuelve 6 para la entrada //[***]\\n1***2***3" do
			@cal.add("//[***]\n1***2***3").should eql 6
		end
		it "Formato de multiples delimitadores; devuelve 6 para la entrada //[***][---]\\n1***2---3" do
			@cal.add("//[***][---]\n1***2---3").should eql 6
		end
	end

end```